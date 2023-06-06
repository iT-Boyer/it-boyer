+++
title = "gptel 的 api 是如何插入到当前 buffer"
description = "博客简介"
date = 2023-05-27T03:12:00+08:00
lastmod = 2023-05-27T03:13:04+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

模拟curl 方式请求slack Api,需要参考的逻辑有 

1.  [ ] 先定位入口，跟踪代码
2.  定位到 gptel-curl 实现类主要功能都在这里，需要在这里找到解决方案先分析该类的主要功能 
    1.  参数内容的收集 `gptel-curl--get-args`
    2.  以进程的方式开始 curl 请求数据开启进程 `gptel-curl-get-response`
    3.  处理数据：以流的方式插入到当前 buffer
3.  需要流式响应功能支持，socket模式 [Socket Mode implementation | Slack](https://api.slack.com/apis/connections/socket-implement)


## gptel 入口 gptel-request 方法介绍 {#gptel-入口-gptel-request-方法介绍}

理解源码，分析入口参数和环境变量初始状态，以及后续功能方法的处理逻辑 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(cl-defun gptel-request
    (&optional prompt &key callback
               (buffer (current-buffer))
               position context (stream nil)
               (in-place nil)
               (system gptel--system-message))
  (let* ((gptel-stream stream)
         (start-marker
          (cond
           ((null position)
            (if (use-region-p)
                (set-marker (make-marker) (region-end))
              (point-marker)))
           ((markerp position) position)
           ((integerp position)
            (set-marker (make-marker) position buffer))))
         (full-prompt
          (cond
           ((null prompt) (gptel--create-prompt start-marker))
           ((stringp prompt)
            `((:role "system" :content ,system)
              (:role "user"   :content ,prompt)))
           ((consp prompt) prompt)))
         (info (list :prompt full-prompt
                     :buffer buffer
                     :position start-marker)))
    (when context (plist-put info :context context))
    (when in-place (plist-put info :in-place in-place))
    (funcall
     (if gptel-use-curl
         #'gptel-curl-get-response #'gptel--url-get-response)
     info callback)))
```

这是 gptel-request 函数的定义,它向 ChatGPT 发出请求并获取响应。让我们详细分析它: 

1.  它接受可选的 `prompt` 、 `callback` 和许多关键字参数。

2.  `prompt` 可以是字符串(用于构造完整提示)、 `nil` (使用当前区域或缓冲区内容)或 `plist` 的列表(原样使用)。

3.  `callback` 是两个参数的函数,用于响应(字符串)和 `info` `plist` 调用: 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (callback RESPONSE INFO)
    ```
    如果没有响应或错误, RESPONSE 为 nil。 
    
    info plist 至少包含: 
    
    -   `:prompt` - 发送的完整提示
    -   `:position` - 发送请求的 marker
    -   `:buffer` - 发送请求时的当前缓冲区
    -   `:status` - 请求结果的简短字符串

4.  如果省略 callback ,响应将插入发送请求的点。

5.  BUFFER 是请求属于的缓冲区。如果省略,则记录当前缓冲区。

6.  POSITION 是缓冲区位置(整数或标记)。如果省略,则记录(point)或(region-end),具体取决于是否激活了区域。

7.  CONTEXT 是回调运行所需的任何其他数据。它包含在回调的INFO参数中。

8.  SYSTEM 是发送给 ChatGPT 的系统消息(聊天指令)。如果省略,则使用当前缓冲区的 `gptel--system-message` 的值。

9.  以下关键字主要用于内部使用: 
    -   `IN-PLACE` :是一个布尔值,用于默认回调在插入响应时确定是否需要在提示和响应之间插入分隔符。
    -   `STREAM` : 是一个布尔值,用于确定是否应流式传输响应,就像在\`gptel-stream’中一样。如果指定自定义CALLBACK,请勿设置此项!

10. 该函数调用周围可以 let 绑定模型参数。

所以总而言之,这个函数向 ChatGPT 发出请求并获取响应,要么使用提供的回调进行处理,要么直接将其插入到缓冲区中。它接受许多参数以控制和定制该请求。 


### 使用 {#使用}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun gptel-tmp-request (prompt)
  (when (string= prompt "") (user-error "A prompt is required."))
  (gptel-request prompt
                 :callback (lambda (response info)
                             (if (not response)
                                 (message "gptel-capture 错误: %s" (plist-get info :status))
                               ;; (with-current-buffer
                               (goto-char (point-max))
                               (unless (bolp)
                                 (insert "\n"))
                               (insert "**  测试 \n" response "\n")
                               ;; )
                               ))
                 ))
;;测试 gptel-tmp-request
(let ((prompt "测试"))
  (gptel-tmp-request prompt)
  )
```

```text
gptel-curl--sentinel
```


## gptel-curl 中的方法介绍 {#gptel-curl-中的方法介绍}


### 参数部分 {#参数部分}

要先理解 gptel 参数格式，按照格式整理出 slack 接口的入参变量 

1.  gptel 参数格式 
    <a id="code-snippet--gptelarg"></a>
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun gptel-curl--get-args (prompts token)
         "Produce list of arguments for calling Curl.
    
       PROMPTS is the data to send, TOKEN is a unique identifier."
         (let* ((args
                 (list "--location" "--silent" "--compressed" "--disable"))
                (url "https://api.openai.com/v1/chat/completions")
                (data (encode-coding-string
                       (json-encode (gptel--request-data prompts))
                       'utf-8))
                (headers
                 `(("Content-Type" . "application/json")
                   ("Authorization" . ,(concat "Bearer " (gptel--api-key))))))
           (push (format "-X%s" "POST") args)
           (push (format "-w(%s . %%{size_header})" token) args)
           ;; (push (format "--keepalive-time %s" 240) args)
           (push (format "-m%s" 60) args)
           (push "-D-" args)
           (pcase-dolist (`(,key . ,val) headers)
             (push (format "-H%s: %s" key val) args))
           (push (format "-d%s" data) args)
           (nreverse (cons url args))))
    ```

这是 `gptel-curl--get-args` 函数的定义,它为调用 `curl` 生成参数列表。让我们详细分析它: 

1.  它接受 `prompts` (要发送的数据)和 `token` (唯一标识符)作为参数。
2.  它首先定义一些默认 `curl args` ,如 `--location` 、 `--silent` 、 `--compressed` 等。
3.  它定义 ChatGPT API端点的URL。
4.  它使用 `json-encode` 将 `prompts` 编码为 JSON,并使用 `encode-coding-string` 将其编码为 UTF-8。
5.  它定义包含认证令牌的标题。
6.  它使用 `push` 将更多 `args` 添加到列表,如-X POST(用于POST请求)、-w 来关联响应大小与 token、-m 60(超时)等。
7.  它使用 `pcase-dolist` 迭代标题,并使用 push 将格式化的 `-H` args 添加到列表中。
8.  它使用 push 将 -d arg (包含编码的JSON数据)添加到列表中。
9.  它使用 nreverse 反转 args 列表,并使用 cons 将 URL 添加到开头,形成完整的 curl 调用参数。
10. 它返回生成的 args 列表。

所以总而言之,这个函数构造用于将 prompts 发送到 ChatGPT API的curl命令行参数。它添加所有必需的参数,如方法、数据、标头、超时等,并返回完整的参数列表以供调用 curl 使用。 

它显示了构建命令行工具包装器/接口所需的各种知识,如: 

-   curl 的参数理解
-   编码(这里为JSON和UTF-8)
-   plist 和 alist 的使用
-   反转列表以获得正确的参数顺序

<!--listend-->

```elisp
(gptel-curl--get-args "xx" "xxx3")
```

|           |              |          |            |        |                           |      |     |                                  |                                                                             |                                                                                  |                                              |
|-----------|--------------|----------|------------|--------|---------------------------|------|-----|----------------------------------|-----------------------------------------------------------------------------|----------------------------------------------------------------------------------|----------------------------------------------|
| --disable | --compressed | --silent | --location | -XPOST | -w(xxx3 . %{size_header}) | -m60 | -D- | -HContent-Type: application/json | -HAuthorization: Bearer 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 | -d{"model":"gpt-3.5-turbo","messages":[120,120],"stream":true,"temperature":1.0} | <https://api.openai.com/v1/chat/completions> |


#### slack 相关参数 {#slack-相关参数}

整理 slack 参数，打印测试 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun slack-curl--get-args (prompts token)
  "Produce list of arguments for calling Curl.

PROMPTS is the data to send, TOKEN is a unique identifier."
  (let* ((args (list "--location" "--silent" "--compressed" "--disable"))
         (url "https://slack.com/api/chat.postMessage")
         (data (encode-coding-string
                (json-encode (gptel--request-data prompts))
                'utf-8))
         (headers
          `(("Content-Type" . "application/json")
            ("Authorization" . ,(concat "Bearer " (gptel--api-key))))))
    ;;向 crul参数：args 中添加字段
    (push (format "-X%s" "POST") args)
    (push (format "-w(%s . %%{size_header})" token) args)
    ;; (push (format "--keepalive-time %s" 240) args)
    (push (format "-m%s" 60) args)
    (push "-D-" args)
    ;; pcase-dolist essentially循环一个alist或plist,为每个元素执行一些操作
    (pcase-dolist (`(,key . ,val) headers)
      (push (format "-H%s: %s" key val) args))
    ;;添加 prompt data
    (push (format "-d%s" data) args)
    (nreverse (cons url args))))
```


### gptel-curl 进程部分 {#gptel-curl-进程部分}

先理解源码，看看如何新建 slack API 的进程，实现异步请求接口，同时和 emacs 交互。 

这个函数启动一个后台 `curl` 进程来获取响应,并设置回调以在获得完整响应时进行处理。它还包括将响应从 markdown 转换为 org mode 的选项。 

它显示了 `Emacs` 中的强大进程和异步编程功能。这使您可以启动长时间运行的后台进程,同时仍然保持 `Emacs` 响应,因为处理是异步完成的。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;;TODO: The :transformer argument here is an alternate implementation of
;;`gptel-response-filter-functions'. The two need to be unified.
;;;###autoload
(defun gptel-curl-get-response (info &optional callback)  ;; 1
  "Retrieve response to prompt in INFO.

INFO is a plist with the following keys:
- :prompt (the prompt being sent)
- :buffer (the gptel buffer)
- :position (marker at which to insert the response).

Call CALLBACK with the response and INFO afterwards. If omitted
the response is inserted into the current buffer after point."
  (let* ((token (md5 (format "%s%s%s%s"
                             (random) (emacs-pid) (user-full-name)
                             (recent-keys))))   ;; 3
         (args (gptel-curl--get-args (plist-get info :prompt) token)) ;;
         (process (apply #'start-process "gptel-curl"
                         (generate-new-buffer "*gptel-curl*") "curl" args))) ;;4
    (with-current-buffer (process-buffer process)
      (set-process-query-on-exit-flag process nil)  ;;6
      (setf (alist-get process gptel-curl--process-alist)
            (nconc (list :token token
                         :callback (or callback
                                       (if gptel-stream
                                           #'gptel-curl--stream-insert-response
                                         #'gptel--insert-response))
                         :transformer (when (or (eq gptel-default-mode 'org-mode)
                                                (eq (buffer-local-value
                                                     'major-mode
                                                     (plist-get info :buffer))
                                                    'org-mode))
                                        (gptel--stream-convert-markdown->org)))
                   info))
      (if gptel-stream
          (progn (set-process-sentinel process #'gptel-curl--stream-cleanup)
                 (set-process-filter process #'gptel-curl--stream-filter))  ;;8
        (set-process-sentinel process #'gptel-curl--sentinel)))))  ;;9
```

1.  它定义了一个名为 `gptel-curl-get-response` 的函数,该函数接受 `info plist` 和可选的回调 `callback` 作为参数。
2.  `info plist` 包含诸如提示、缓冲区、插入响应位置的标记等信息。 `callback` 将在获得响应后以 `response` 和 `info` 为参数进行调用。如果省略,响应将插入当前缓冲区的 `point` 之后。
3.  它生成一个随机 `token`,并使用该 `token` 和 `prompt` 构建 `curl` 命令行参数。
4.  它使用 `apply` 启动名为 `gptel-curl` 的新进程来运行 `curl` 和构建的参数。输出发送到一个专用缓冲区 `*gptel-curl*` 。
5.  它在 `gptel-curl` 进程的缓冲区中设置一些进程标志:
6.  查询退出标志设置为 `nil`
7.  它在 `gptel-curl--process-alist` 中存储进程、 `token` 、回调和转换器(如果需要,用于将 `markdown` 转换为 `org-mode`)
8.  如果 `gptel-stream` 为真,它设置 `sentinel` 和 `filter` 函数来处理流输出。否则它设置 `sentinel` 函数。
9.  `sentinel` 和 `filter` 函数(定义在其他地方)将处理完成响应的接收和调用回调。


#### 设置回调方法：gptel-curl--stream-insert-response {#设置回调方法-gptel-curl-stream-insert-response}

这行代码是对函数 `gptel-curl--stream-insert-response` 的引用。 `#’` 是读取函数名称的语法。 

所以这一行实际上表示要调用名为 `gptel-curl--stream-insert-response` 的函数。 

该函数很可能与此前显示的 `gptel-curl-get-response` 函数一起工作,因为: 

1.  它也与 `gptel-curl` 进程相关,用于从 `curl` 获取响应。
2.  `gptel-curl-get-response` 指定如果 `gptel-stream` 为真,则使用 `gptel-curl--stream-insert-response` 作为回调。

所以我们可以推断, `gptel-curl--stream-insert-response` 函数很可能执行以下操作: 

1.  它是 `gptel-curl-get-response` 指定的回调函数,以在接收到完整响应时进行调用。
2.  鉴于其名称,它可能将响应插入 `calling` 函数(`gptel-curl-get-response`)指定的缓冲区。
3.  因为它是 `stream` 版本,它可能逐渐添加响应,而不是等待完整响应。这可能意味着它被设置为 `gptel-curl` 进程的 `filter` 函数,每当有输出可用时就被调用。
4.  它可能还执行 `markdown` 到 `org mode` 的转换(如果需要),因为 `gptel-curl-get-response` 指定了该选项。

所以总而言之,这行代码表明在处理流 `curl` 响应并将其插入指定缓冲区时要调用名为 `gptel-curl--stream-insert-response` 的函数。该函数很可能通过 `filter` 函数挂钩到相关进程,并在处理响应时执行任何必要的格式转换。 


#### 在 `gptel-curl--process-alist` 中存储进程 {#在-gptel-curl-process-alist-中存储进程}

这段代码将一些值与 `gptel-curl` 进程相关联,并将其存储在 `gptel-curl--process-alist` 中。让我们详细分析它: 

1.  它使用 `alist-get` 查找与进程相关联的值。如果尚不存在,它将返回 `nil` 。
2.  它使用 `nconc` 将多个列表合并为一个列表。它构建要与进程相关联的列表,包含: 
    1.  `:token` - 之前生成的随机 token
    2.  `:callback` - 要使用的回调。它首先检查是否指定了 `callback` ,如果没有则使用 `gptel-stream` 的值来确定是使用 `gptel-curl--stream-insert-response` 还是 `gptel--insert-response` 。
    3.  `:transformer` - 仅当 `gptel-default-mode` 或 `info` 指定的缓冲区主要模式为 `org-mode` 时,才使用 `gptel--stream-convert-markdown-&gt->org` 进行 `markdown` 到 `org` 模式的转换。
3.  它将 `info plist` 附加到列表的末尾。
4.  它使用 `setf` 将该组合列表存储在 `gptel-curl--process-alist` 中,与进程相关联。

所以总而言之,这段代码为进程设置一个回调函数和转换器(如果需要),并将所有这些信息与一个随机 `token` 相关联,以防止跨站点请求伪造。它存储所有这些信息,以便以后可以基于进程查找其回调/转换器等。 

这显示了 `Lisp` 的许多功能,如: 

-   `plist` 的使用
-   `nconc` 用于列表连接
-   `setf` 用于存储值
-   以 `alist` 形式存储关联数据
-   有条件的逻辑来设置可选值

这些构建块允许您构建复杂的数据结构和关联,这是编写实际应用程序所必需的。 


#### 定义自己的函数 {#定义自己的函数}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;;;###autoload
(defun slack-curl-get-response (info &optional callback)  ;; 1
  "Retrieve response to prompt in INFO.

INFO is a plist with the following keys:
- :prompt (the prompt being sent)
- :buffer (the gptel buffer)
- :position (marker at which to insert the response).

Call CALLBACK with the response and INFO afterwards. If omitted
the response is inserted into the current buffer after point."
  (let* ((token (md5 (format "%s%s%s%s"
                             (random) (emacs-pid) (user-full-name)
                             (recent-keys))))   ;; 3
         (args (gptel-curl--get-args (plist-get info :prompt) token)) ;;
         (process (apply #'start-process "gptel-curl"
                         (generate-new-buffer "*gptel-curl*") "curl" args))) ;;4
    (with-current-buffer (process-buffer process)
      (set-process-query-on-exit-flag process nil)  ;;6
      (setf (alist-get process gptel-curl--process-alist)
            (nconc (list :token token
                         :callback (or callback
                                       (if gptel-stream
                                           #'gptel-curl--stream-insert-response
                                         #'gptel--insert-response))
                         :transformer (when (or (eq gptel-default-mode 'org-mode)
                                                (eq (buffer-local-value
                                                     'major-mode
                                                     (plist-get info :buffer))
                                                    'org-mode))
                                        (gptel--stream-convert-markdown->org)))
                   info))
      (if gptel-stream
          (progn (set-process-sentinel process #'gptel-curl--stream-cleanup)
                 (set-process-filter process #'gptel-curl--stream-filter))  ;;8
        (set-process-sentinel process #'gptel-curl--sentinel)))))
```


### 以流的方式插入 buffer {#以流的方式插入-buffer}

先理解源码，如何把 curl 进程的响应数据以流的方式插入到 buffer 中 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;;;###autoload
(defun gptel-curl--stream-insert-response (response info)
  "Insert streaming RESPONSE from ChatGPT into the gptel buffer.

INFO is a mutable plist containing information relevant to this buffer.
See `gptel--url-get-response' for details."
  (let ((status-str  (plist-get response :status))
        (start-marker (plist-get info :position))
        (tracking-marker (plist-get info :tracking-marker))
        (transformer (plist-get info :transformer)))
    (when response
        (with-current-buffer (marker-buffer start-marker)
          (save-excursion
            (message "插入结果222：-----%s \n 状态：%s" response status-str)
            (unless tracking-marker
              (gptel--update-header-line " Typing..." 'success)
              (goto-char start-marker)
              (unless (or (bobp) (plist-get info :in-place))
                (insert "\n\n"))
              (setq tracking-marker (set-marker (make-marker) (point)))
              (set-marker-insertion-type tracking-marker t)
              (plist-put info :tracking-marker tracking-marker))

            (when transformer
              (setq response (funcall transformer response)))

            (put-text-property 0 (length response) 'gptel 'response response)
            (goto-char tracking-marker)
            (insert response))))))
```

这是gptel-curl--stream-insert-response函数的定义,如我们预期的那样,它将流响应插入指定的缓冲区。让我们详细分析它: 

1.  它接受响应response和info plist作为参数。info plist包含与该缓冲区相关的信息,如gptel--url-get-response中详述。

2.  它从响应中获取状态字符串,从info中获取起始标记、跟踪标记和转换器(如果有)。

3.  如果有响应,它将在包含起始标记的当前缓冲区中进行操作。

4.  它首先检查是否已设置跟踪标记。如果没有,它会更新标题行,转到起始标记,插入两个换行符(除非在原地编辑),并设置一个跟踪标记以跟踪响应的插入位置。它还将跟踪标记添加到info plist中。

5.  如果指定了转换器,它将对响应进行转换。

6.  它将gptel属性与整个响应文本关联。

7.  它转到跟踪标记并插入响应。

8.  然后它返回,直到有更多响应要添加,在那时它将再次调用。

所以总而言之,这个函数将流式响应添加到缓冲区,进行任何必要的转换,并跟踪每个响应的插入位置以供将来添加使用。 

它显示了Emacs Lisp的许多强大功能,如: 

-   标记和缓冲区操作
-   属性和文本属性
-   plist的使用
-   save-excursion用于临时保存位置

这是一个非常典型的Emacs Lisp函数,展示了构建复杂功能所需的各种工具。 


## 使用 slack curl {#使用-slack-curl}


### 参数定义模块 {#参数定义模块}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }

(defun slack-curl--get-args (prompts)
  "Produce list of arguments for calling Curl.

PROMPTS is the data to send, TOKEN is a unique identifier."
  (let* ((args (list "--location" "--silent" "--compressed" "--disable"))
         (data (encode-coding-string (url-hexify-string "你好") 'utf-8))
         ;; (url (format "https://slack.com/api/chat.postMessage?channel=D052Y13FSRY&as_user=true&text=%s" data))
         (url "https://slack.com/api/conversations.history?channel=D052Y13FSRY&limit=2")
         (headers
          `(("Authorization" . ,(concat "Bearer " slack-claude-bot-token)))))
    ;;向 crul参数：args 中添加字段
    (push (format "-X%s" "POST") args)
    ;; (push (format "-w(%s . %%{size_header})" token) args)
    ;; (push (format "--keepalive-time %s" 240) args)
    (push (format "-m%s" 60) args)
    (push "-D-" args)
    ;; pcase-dolist essentially循环一个alist或plist,为每个元素执行一些操作
    (pcase-dolist (`(,key . ,val) headers)
      (push (format "-H%s: %s" key val) args))
    ;;添加 prompt data
    ;; (push (format "-d%s" data) args)
    (nreverse (cons url args))))
```


### 数据解析模块 {#数据解析模块}

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun slack-curl--stream-filter (process output)
  (message "进程过滤----")
  (let* ((content-strs)
         (proc-info (alist-get process slack-curl--process-alist)))
    (with-current-buffer (process-buffer process)
      ;; Insert output
      (save-excursion
        (goto-char (process-mark process))
        (insert output)
        (set-marker (process-mark process) (point)))

      ;; Find HTTP status
      (unless (plist-get proc-info :http-status)
        (save-excursion
          (goto-char (point-min))
          (when-let* (((not (= (line-end-position) (point-max))))
                      (http-msg (buffer-substring (line-beginning-position)
                                                  (line-end-position)))
                      (http-status
                       (save-match-data
                         (and (string-match "HTTP/[.0-9]+ +\\([0-9]+\\)" http-msg)
                              (match-string 1 http-msg)))))
            (plist-put proc-info :http-status http-status)
            (plist-put proc-info :status (string-trim http-msg))))
        ;; Handle read-only gptel buffer
        (when (with-current-buffer (plist-get proc-info :buffer)
                (or buffer-read-only
                    (get-char-property (plist-get proc-info :position) 'read-only)))

          (message "Buffer is read only, displaying reply in buffer \"*ChatGPT response*\"")
          (display-buffer
           (with-current-buffer (get-buffer-create "*ChatGPT response*")
             (goto-char (point-max))
             (move-marker (plist-get proc-info :position) (point) (current-buffer))
             (current-buffer))
           '((display-buffer-reuse-window
              display-buffer-pop-up-window)
             (reusable-frames . visible)))))

      (when-let ((http-msg (plist-get proc-info :status))
                 (http-status (plist-get proc-info :http-status)))

        ;; Find data chunk(s) and run callback
        (when (equal http-status "200")
          (funcall #'slack-curl--stream-insert-response
                   (let* ((json-object-type 'plist)
                          (status (string-match "}}$" (buffer-string)))
                          (response) (content-str))
                     (message "匹配结果：%s" status)
                     (when (string-match "}}" (buffer-string))
                         (setq response (replace-regexp-in-string "[^\\{]*?\{\"ok" "{\"ok"(buffer-string)))
                       (with-temp-buffer
                         (insert response)
                         (goto-char (point-min))
                         (let* ((json-object-type 'plist)
                                (json-array-type 'list)
                                (json (json-read))
                                (messages (plist-get json :messages))
                                )
                           ;; 解析 messages 消息数据
                           (dolist (msg messages)
                             ;; 仅解析Claude的信息
                             (if-let* ((bot (plist-get msg :bot_profile))
                                       (botname (plist-get bot :name))
                                       (claude (string= botname "Claude"))
                                       (text (plist-get msg :text)))
                                 (push text content-strs)
                               ))
                           )
                         )
                       )
                     (apply #'concat (nreverse content-strs)))
                   proc-info))))))


;; (condition-case nil
                     ;;     (while (re-search-forward "\{\"ok" nil t)
                     ;;     (save-match-data
                     ;;       (unless (looking-at "\}{3}$")
                     ;;         (when-let* ((response (json-read))
                     ;;                     (delta (map-nested-elt
                     ;;                             response '(:choices 0 :delta)))
                     ;;                     (content (plist-get delta :content)))
                     ;;           (message "响应plist：-----%s" (buffer-string))
                     ;;           (message "delta：-----%s" delta)
                     ;;           (message "解析content：-----%s" content)
                     ;;           (push content content-strs)
                     ;;           (message "组合结果：%s" content-strs)
                     ;;           ))))
                     ;;   (error
                     ;;    (goto-char (match-beginning 0))))
```

