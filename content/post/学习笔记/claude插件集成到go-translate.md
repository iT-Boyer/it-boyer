+++
title = "写一个 claude emacs 翻译工具客户端"
description = "博客简介"
date = 2023-05-25T02:29:00+08:00
lastmod = 2023-05-25T16:17:37+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

目标：借助claude 的结构，请求结果，然后插入到当前位置。 

现在已经在 go-translate 找到了最佳的交互方式，只要通过 elisp 调用 slack api 就可以实现翻译工具的扩展。 

现在要做的几件事情 

1.  阅读 org-translate 翻译引擎的扩展方法
2.  使用 elisp 实现 slack api ，获取 claude 的回答。
3.  后续：不仅翻译，还可以通过 prompt 设置不同的角色，和 capture 整合，和 agenda 整合的，都迎刃而解了。

实现 claude 基于 web api 使用轮询的方式获取 claude 的回复内容，然后给 gts render 中。 

过程中需要了解 elisp 语法，了解 go-translate 源码,根据 readme 基本了解设计思路之后，开始开发 claude 引擎。 

先参考已实现的引擎，在作者的实现中找灵感，思路。可以先从 youdao 半成品来了解基本的架构。 

以开发引擎为主，主要包含几个方面 

1.  引擎类属性： 是定义参数，暴漏给用户，设置必要的个人信息和权限的内容。
2.  引擎解析器：需要先声明类，在通过 gts 框架中提供的模型，实行对元数据的分析。
3.  引擎本体：主要和 api 交互，难道数据源，交付给解析器，然后渲染操作。


## 按照思路简单模式实现接口逻辑 {#按照思路简单模式实现接口逻辑}

使用 elisp 实现 http API 调用，返回值为实现slack 接口调用 

在写引擎本体的过程中，面对的挑战： 

1.  获取用户的输入,存在task中： 获取方式 `(oref task text)` ，具体要了解 task 结构，它提供了很多重要的信息。解析完成的内容最终存在这里。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defclass gts-task ()
         ((id         :initform (gensym))
    
          (text       :initarg :text :documentation "Text that sent to engines. It maybe contains delimiters")
          (from       :initarg :from)
          (to         :initarg :to)
    
          (raw        :initform nil :documentation "raw result responsed by the http-client")
          (parsed     :initform nil :initarg :parsed :documentation "result parsed by parser, string or list")
          (err        :initform nil :initarg :err    :documentation "error info")
          (meta       :initform nil :initarg :meta   :documentation "extra info passed from parser to render. tbeg/tend")
    
          (engine     :initarg :engine     :initform nil)
          (render     :initarg :render     :initform nil)
          (translator :initarg :translator :initform nil)))
    ```
2.  claude API 集成因为是基于 claude web api 聊天接口，这个过程设计到两个接口，一个是发送用户信息给claude，开启一次聊天。然后，通过一个获取聊天历史记录的接口中获取 claude 的聊天记录。 
    
    claude 回复是断续的，就像 chatgpt 在聊天过程中，不断输入的状态，所以从聊天记录接口中获取的内容也就是不断更新的。需要用到轮询的方式，等待 claude 输入完成，之后，表示一次聊天成功。 
    
    基于这种状态，就需要用到聊个前嵌套的接口来实现接口调用。 
    
    先说思路：第一先使用发送用户信息的接口，创建会话，等会话回调成功，再调用聊天历史接口，开始定时轮询，等待回复完成。
3.  封装请求方法, 支持 对话，和 获取回复的两个接口。 
    1.  认证方式：Authorization ，需要新建机器人，获取相关权限和 token
    2.  新建对话 API 和 入参是 `chat.postMessage?channel=SlackChannel&as_user=true&text=Text`
    3.  通过历史记录获取回复的借口： `?channel=SlackChannel&limit=limit&pretty=1&oldest=oldest`


## 借助 babel 实现简单的逻辑 {#借助-babel-实现简单的逻辑}

在 elisp 中声明一个请求 http API的接口，该接口返回一个字符串，前三次返回的内容以“\__typeing…“结尾，第四次返回不含“\__typeing…“。请一个实现http请求，递归发出请求，直到接口返回的内容不含“\__typeing…“字符时，停止，然后返回最后一次响应的结果。 

函数1 作为 API 入口，函数2 作为第一次请求的数据 函数3 作为第二次请求的数据 让后调用API 处理结果返回。 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; 函数1 实现作为API 入口
(cl-defmethod translate1 (text task)
  ;;拿到初始数据，返回翻译结果
  (message "启动翻译引擎")
  (talkaction text
              (lambda ()
                ;;talk完成回调
                (message "talk 完成回调--")
                (message "开始下一个任务%s" task)
                ))
  )

(defun talkaction(text block)
  (message "创建对话")
  ;;开始请求服务器，处理业务
  (requestServer text
                 (lambda (result)
                   (message "原始数据：%s \n 处理数据%s" text result)
                   (funcall block)
                   ;; (funcall translateblock)
                   ))
  )
(defun requestServer (text  blocke)
  (message "服务器---")
  (let ((result (format "%s成功" text)))
    ;;在执行回调闭包
    (funcall blocke result)
    )
  )

(translate1 "你好" "订阅监听")
```

```text
开始下一个任务订阅监听
```


## 在使用 elisp 闭包嵌套出现的问题 {#在使用-elisp-闭包嵌套出现的问题}

在两个定义两个函数相互调用，通过闭包的方式传递上下文环境过程中，出现在网络回调的闭包中无法访问入参闭包的情况。 

代码如下： 

1.  新建对话的函数，入参包含从引擎中传递的 `done` 闭包，在回调中 `(funcall done)` ,就会出现上述的问题，出现对话失败的提示。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun new-claude-talk (engine done)
         (with-slots (talkurl auth-key channel user talk-time) engine
           (setq turl (format talkurl channel (url-hexify-string user)))
           (setf talk-time (float-time (car (current-time))))
           (gts-do-request turl
                           :headers `(("Authorization" . ,(format "Bearer %s" auth-key)))
                           :done (lambda ()
                                   (funcall done)
                                   )
                           :fail (lambda (err)
                                   (message "对话失败:%s" err)
                                   ))
           )
         )
    ```
2.  引擎中的代码实现这是引擎的实现入口，调用上一步的网络请求，发起会话。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (cl-defmethod gts-translate-old ((engine gts-claude-engine) task rendercb)
         (with-slots (user talk-time) engine
           (setf  user (oref task text))
           (message "用户正式输入：%s" (oref engine user))
           (gts-with-token engine
             (lambda ()
               (message "user对话内容")
               (with-slots (text) task
                 ;; 获取时间戳
                 (message "对话时间：%s" (oref engine talk-time))
                 (message "对话内容：%s" text)
                 )
               (subscribe-claude-request engine talk-time
                                         (lambda ()
                                           (message "完成开始绘制 -- ")
                                           (gts-update-raw task (buffer-string))
                                           (gts-parse (oref engine parser) task)
                                           (funcall rendercb)
                                           )
                                         )
               )
             (lambda (err)
               (gts-render-fail task
                 (format "获取令牌密钥时出错，请检查您的网络和代理，或稍后重试\n\n%s" err))))
           )
         )
    ```
3.  轮询接口的实现，在第一步新建会话完成时，会触发方法的调用。主要依赖第一个接口回调回来的时间戳，其实在分析之口，为了避免传参问题，时间戳完全可以获取当前时间。 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun subscribe-claude-request (engine time done fail)
         "Request an HTTP API and return response when it does not end with '__typing...'."
         (with-slots (subscribeurl auth-key channel) engine
           (message "获取时间戳：%s" time)
           (let ((json)
                 (subscribe (format subscribeurl channel time)))
             (gts-do-request subscribe
                             :headers `(("Authorization" . ,(format "Bearer %s" auth-key)))
                             :done (lambda ()
                                     (let (beg end json)
                                       (re-search-forward "^[0-9]+$")
                                       (setq beg (point))
                                       (re-search-forward "^\\([0-9]+\\)$")
                                       (setq end (- (point) (length (match-string 1))))
                                       (setq json (json-read-from-string (string-trim (buffer-substring-no-properties beg end))))
                                       json))
                             :fail fail)
             (dolist (object (plist-get json :messages))
               (setq oldest (plist-get object :ts))
               (setq result (plist-get object :text)))
             (if (or (= (length result) 0)
                     (string-match "_Typing\\.\\.\\._" result))
                 ;; 如果响应以 _typing..._ 结尾,递归调用
                 (subscribe-claude-request engine time done fail)
               ;; 否则直接返回响应
               )
             (funcall done)
             )))
    ```
    获取当前时间戳的脚本 
    ```elisp
       (time-to-seconds (current-time))
    ```
    
    ```text
    1684952067.575853
    ```

该代码将返回从Unix纪元（1970年1月1日UTC）开始到当前时间的秒数。 

