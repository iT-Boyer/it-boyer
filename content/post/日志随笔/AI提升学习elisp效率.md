+++
title = "借助AI修复插件中中文乱码问题"
description = "博客简介"
date = 2023-04-19T13:56:00+08:00
lastmod = 2023-04-19T15:04:30+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

在使用org-sync 过程中，对中文解析问题，导致无法正常同步。 

现在尝试通过让AI 阅读elisp 源码，给出解决办法，和编写可用的脚本。 

AI对话需要解决的两个问题：先问为什么会同步失败。 

1.  emacs org-sync 同步github issue 中文乱码。
2.  分析org-sync插件源码，确认导致中文乱码的片段，并给出修复方法

下面是同步github-issue 核心代码，可以交给AI 帮你分析问题： 

通过分析这个 org-sync-github.el 的源码,我发现以下几个地方可能导致中文乱码: 

1.  在发送 HTTP 请求时,Accept-Encoding 没有设置为 utf-8,这会导致 GitHub 返回的 JSON 数据的编码不是 utf-8,从而解析后出现乱码: 
    ```elisp
       (defun org-sync-github-request (method url &optional data)
         "Send HTTP request at URL using METHOD with DATA.
       Return the server decoded JSON response."
         (message "%s %s %s" method url (prin1-to-string data))
         (let* ((url-request-method method)
                (url-request-data data)
                (buf (org-sync-github-url-retrieve-synchronously url)))
    ```
    (url-request-extra-headers '(("Accept-Encoding" . "utf-8")))

修复方法是在请求头中添加: 

```elisp
(setq url-request-extra-headers
      '(("Accept-Encoding" . "utf-8")))
```

1.  获取响应内容后,没有对 JSON 数据做解码,直接解析了原始的字符数据:

<!--listend-->

```elisp
(with-current-buffer buf
  (goto-char url-http-end-of-headers)
  (prog1 (json-read) (kill-buffer)))))
```

修复方法是先对响应做解码,再解析 JSON: 

```elisp
(with-current-buffer buf
  (let ((content (decode-coding-string
                  (buffer-string) 'utf-8))))
    (goto-char url-http-end-of-headers)
    (prog1 (json-read content) (kill-buffer)))))
```

1.  解析 JSON 时没有设置 ensure_ascii=False,无法正确解析中文:

<!--listend-->

```elisp
(json-read)
```

修复方法是: 

```elisp
(json-read-from-string content 'utf-8 :ensure-ascii nil)
```

1.  在展示内容时,buffer 的默认编码未设置为 utf-8,导致无法正确显示中文:

修复方法是: 

```elisp
(set-buffer-file-coding-system 'utf-8)
```

以上就是我在这个 `org-sync-github.el`  源码中发现的一些可能导致中文乱码的地方,和对应的修复思路。希望这些建议能够帮助修复此问题。如果还有其它需要分析的地方,请告知我。 （已编辑） 


## 修复问题 {#修复问题}

有了 AI 的分析，就可以 `fork` 插件源码，动手写代码验证，修复问题。 

1.  fork 代码， clone 到本地
2.  按照AI 建议的4个步骤注意修复
3.  编译运行，实现中文支持

<!--listend-->

```elisp

```


### 下载显示乱码 {#下载显示乱码}

对整个文件解析，处理中文问题时，可以通过一样代码解决： 

```elisp { linenos=true, linenostart=1, hl_lines=["6-6"] }
(with-current-buffer
      (url-retrieve-synchronously "http://localhost:1313/iDocs/habit.txt")
  (goto-char (point-min))
  (re-search-forward "^$")
  (delete-region (point) (point-min))
  (decode-coding-string (buffer-string) 'utf-8))
```

url-retrieve 下载的问题件，包含两份内容。如何针对 json 部分 使用 json-read 方法解析，就需要先单独获取 json 部分，然后先转码在解析，产能够正常展示。 

1.  下载 JSON 页面内容
2.  定位到 HTTP header 结束的位置
3.  提取 header 结束后的正文内容
4.  检测编码并正确解码
5.  将内容赋值给变量

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((json-string))
  (let ((download-buffer (url-retrieve-synchronously url)))
    (with-current-buffer download-buffer
      (goto-char (point-min))
      (re-search-forward "^$")
      (forward-char)
      (let ((header-end (point)))
        (let ((coding-system
               (mm-find-buffer-file-coding-system)))
          (setq json-string
                (decode-coding-string
                 ;(buffer-string)
                 (buffer-substring header-end (point-max))
                 coding-system))))
      (kill-buffer))
    json-string))
```

1.  定义 json-string 变量
2.  下载 URL 指定页面内容到 download-buffer
3.  定位到 header 结束位置 header-end
4.  检测下载内容编码 coding-system
5.  提取 header-end 之后的内容,解码为 coding-system 编码
6.  将解码后的字符串赋值给 json-string
7.  返回 json-string

所以,json-string 变量将包含原始的 JSON 字符串内容。同样,要正确处理包含非 ASCII 字符的 JSON 字符串,选择正确的编码至关重要。这可以避免出现乱码等错误。 


### 无法同步到远程 {#无法同步到远程}

根据报错信息和请求数据,问题出现在 Content-Type header 和请求体(body) 中: 

1.  Content-Type 被指定为 text/plain,表示 body 中包含纯文本 
    ```elisp { linenos=true, linenostart=1, hl_lines=["7-8"] }
       (defun org-sync-github-request (method url &optional data)
         "Send HTTP request at URL using METHOD with DATA.
       Return the server decoded JSON response."
         (message "zhe2:%s %s %s" method url (prin1-to-string data))
         (let* ((url-request-method method)
                (url-request-data data)
                (url-request-extra-headers '(("Accept-Encoding" . "utf-8")
                                             ("Content-Type" . " application/json; charset=utf-8")))
                (buf (org-sync-github-url-retrieve-synchronously url)))
    
           (with-current-buffer buf
             (goto-char url-http-end-of-headers)
             (prog1 (json-read) (kill-buffer)))))
    ```

2.  但请求体(body)中包含非 ASCII 字符,如中文。这不符合 text/plain 的要求,会导致错误。 
    
    在代码中添加内容类型 <https://github.com/it-boyer/org-sync/org-sync-github.el> `(encode-coding-string json-str 'utf-8)`

<!--listend-->

```elisp
(defun org-sync-github-bug-to-json (bug)
  "Return BUG as JSON."
  (let ((state (org-sync-get-prop :status bug)))
    (unless (member state '(open closed))
      (error "Github: unsupported state \"%s\"" (symbol-name state))))

  (let* ((title (org-sync-get-prop :title bug))
         (body (org-sync-get-prop :desc bug))
         (assignee (org-sync-get-prop :assignee bug))
         (status (symbol-name (org-sync-get-prop :status bug)))
         (tags (org-sync-get-prop :tags bug)))
    (setq title (decode-coding-string title 'utf-8))
    (setq body (decode-coding-string body 'utf-8))
    (setq assignee assignee)

    (let ((json-str
           (json-encode
            `((title . ,title)
              (body . ,body)
              (assignee . ,assignee)
              (state . ,status)
              (labels . [,@tags])))))
      ;; 格式化为utf-8
      (encode-coding-string json-str 'utf-8)
      )))
```

