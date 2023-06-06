+++
title = "问答AI: ob-swift 导入第三方库"
description = "博客简介"
date = 2023-04-16T02:02:00+08:00
lastmod = 2023-05-08T22:28:28+08:00
categories = ["Inbox"]
draft = false
authors = ["iTBoyer"]
password = ""
+++

## <span class="org-todo todo TODO">TODO</span> 需要验证的问题 {#需要验证的问题}

解决的问题 swift-mode build spm 项目，在repl 中debug spm 代码 ob-swift 默认使用 swift repl buffer ，达到共用 session 的效果 ob-swift 执行命令设置为 swift repl 需要验证的问题：ob-swift 是否和 swift-mode repl 共用 session 

1.  先编译 UnitDemo 项目 ，新建对话 进入 repl session
2.  在 ob-swift 中使用默认的 session ，尝试 import UnitDemo 相关库。
3.  整理 include 方法实现跨块的代码共享参数环境 [Emacs: chaining org babel blocks](https://xenodium.com/emacs-chaining-org-babel-blocks/) [Reddit - Dive into anything](https://www.reddit.com/r/emacs/comments/jjbrvw/chaining_org_babel_blocks/)
4.  整理 noweb 的用法，共享变量 [Noweb Reference Syntax (The Org Manual)](https://orgmode.org/manual/Noweb-Reference-Syntax.html)
5.  整理 ob-babel 官方文档中的 header 参数的应用 [Using Header Arguments (The Org Manual)](https://orgmode.org/manual/Using-Header-Arguments.html)
6.  swift-mode 使用自己的库


## 使用 :session 引入块环境 {#使用-session-引入块环境}

:PROPERTIES: nil:END: 

```swift
let x = 1
let y = 2
let z = x + y
print(z)
```

```swift
let a = 1
```

```swift
print("test1----\(a)")
print("test2----\(a)")
print("test3----\(a)")
```


### 优化 ob-swift session 对话脚本问题 {#优化-ob-swift-session-对话脚本问题}

熟悉几个命令 start-process call-interactively 

1.  源库中使用使用 process

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(let ((obname "*ob-swift-xx*"))
  (start-process obname obname "sudo swift repl")
  )
(let ((obname "*ob-swift-xx*"))
  (process-send-string obname (format "%s\n\"%s\"\n" "print(\"hello\")" ob-swift-eoe))
  (accept-process-output (get-process obname) nil nil 1)
  )
```

1.  fork库中 使用 swift-mode 方法 run-swift

<!--listend-->

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(save-window-excursion
  (let ((obname "*swift-repl*"))
    (setq swift-mode:repl-buffer obname)
    (call-interactively 'run-swift)
    (get-buffer obname)
    )
  )
```

(call-interactively 'run-swift) 方法执行之后，仍提示 no process 是什么原因 

这可能是因为 `run-swift` 方法未能正确启动 Swift 进程。你可以尝试重新启动 Emacs 或检查你是否正确配置了 Swift 环境。你也可以尝试手动启动 Swift 进程以确保它能正常工作。 

如何解决这个问题：ob-swift--eval-in-repl: Buffer **ob-swift-my-session** has no process 


### 设置 ob-swift 默认 session 名称:session "**Swift REPL [swift repl]**" {#设置-ob-swift-默认-session-名称-session-swift-repl-swift-repl}

```swift
print(a)
```


#### 使用 ob-swift 引入第三方库:借助swift-mode:debug-swift-module方法实现 {#使用-ob-swift-引入第三方库-借助swift-mode-debug-swift-module方法实现}

借助 swift-mode:debug-swift-module 方法实现,该方法会新建进程和 swift-repl buffer 它名通过 swift repl 执行的命令命名，例如：\`swift repl -i ...\` 查看进程列表： list-process 查看buffer： spc b B 通过这两个方法可以找到 repl buffer 的名称。 

然后，设置 ob-swift 的 \`：session\` 的名称 `*Swift REPL [swift repl -i ...]*` ,这样，在运行代码块的时候，就可以和 swift-mode 共用环境。 

```swift
import UnitLib
```

这样就可以在代码块中调用第三方库了。 


### 处理 outdata 字符串，截取 //---- 之后的字符串，使用elisp 实现上述功能，举例说明 {#处理-outdata-字符串-截取-之后的字符串-使用elisp-实现上述功能-举例说明}

可以使用 `split-string` 函数将 `outdata` 字符串分割成多个行，并使用 `nthcdr` 和 `member` 函数来找到 `//----` 之后的所有行。以下是一个示例实现： 

在elisp 中通过在指定的字符串分割原始字符串，并得到最后部分，举例说明 

可以使用=split-string=函数来分割字符串。例如，要在字符串"/usr/local/bin"中分割出最后一个部分"bin"，可以使用如下代码： 

```emacs-lisp
(setq outdata "test2----\(a)\nprint(\"test3----\")\n//----\ntest1----1\n326> test2----1\n327> test3----1")
(car (last (split-string outdata "//----\n")))
```

```text
test1----1
326> test2----1
327> test3----1
```

其中， `(split-string "/usr/local/bin" "/")` 将字符串 "_usr/local/bin" 以 "_" 为分隔符分割成一个字符串列表， `last` 函数返回列表中的最后一个元素，然后用 `car` 函数取出这个元素的值。 


## 通过 :noweb 传值 {#通过-noweb-传值}

<a id="code-snippet--c1"></a>
```C
const char * message1 = "hello from block 1";
```

<a id="code-snippet--c2"></a>
```C
const char * message2 = "hi from block 2";
```

```C
#include <stdio.h>

<<c1>>
<<c2>>

int main(void) {
  printf("%s\n", message1);
  printf("%s\n", message2);
  return 0;
}
```

```text
hello from block 1
hi from block 2
```


## 通过 :var 跨源块传递值 {#通过-var-跨源块传递值}

1.  第一步 声明块变量: `,#+name: c_hello`
2.  第二步 在块中引用： `:var data=块变量`

<!--listend-->

<a id="code-snippet--c-hello"></a>
```C
  #include <stdio.h>
  int main(void) { printf("hello C"); return 0; }
```

```sh
  echo hello from sh $data
```

```text
hello from sh hello C
```


## 通过 :include 引用块环境 {#通过-include-引用块环境}

1.  第一步 重写 `adviced:org-babel-execute-src-block`
2.  第二步 声明块变量名： `,#+name: b1`
3.  第三步 引入块环境： `:include 块变量名` 
    ```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
       (defun adviced:org-babel-execute-src-block (&optional orig-fun arg info params)
         (let ((body (nth 1 info))
               (include (assoc :include (nth 2 info)))
               (named-blocks (org-element-map (org-element-parse-buffer)
                                 'src-block (lambda (item)
                                              (when (org-element-property :name item)
                                                (cons (org-element-property :name item)
                                                      item))))))
           (while include
             (unless (cdr include)
               (user-error ":include without value" (cdr include)))
             (unless (assoc (cdr include) named-blocks)
               (user-error "source block \"%s\" not found" (cdr include)))
             (setq body (concat (org-element-property :value (cdr (assoc (cdr include) named-blocks)))
                                body))
             (setf (nth 1 info) body)
             (setq include (assoc :include
                                  (org-babel-parse-header-arguments
                                   (org-element-property :parameters (cdr (assoc (cdr include) named-blocks)))))))
           (funcall orig-fun arg info params)))
    
       ;; 这段代码使用 =advice-add= 函数，在 =org-babel-execute-src-block= 周围添加了一个名为 =adviced:org-babel-execute-src-block= 的 advice。这意味着在执行 =org-babel-execute-src-block= 时，将首先执行此 advice 中定义的代码，然后再执行原始函数中的代码。
       (advice-add 'org-babel-execute-src-block :around 'adviced:org-babel-execute-src-block)
       解释一下这段代码
    ```

<!--listend-->

<a id="code-snippet--b1"></a>
```C
  const char * message1 = "hello from block 1";
```

<a id="code-snippet--b2"></a>
```C
  const char * message2 = "hello from block 2";
```

```C
  #include <stdio.h>
  int main(void) {
    printf("%s\n", message1);
    printf("%s\n", message2);
    return 0;
  }
```

```text
hello from block 1
hello from block 2
```

在运行代码时提示错误：use of undeclared identifier 'message1' printf("%s\n", message1); 

这是因为 `message1` 的作用域仅限于 `b1` 块中，无法在 `main` 函数内部访问。可以将 `message1` 定义成全局变量或使用 `#include` 指令将 `b1` 块中的代码直接包含到 `main` 函数中。例如，将 `b1` 的代码直接放到 `main` 函数上方，或者在 `main` 函数中使用 `#include "file.org::b1"` 来包含 `b1` 块中的代码。 


## 自定义 ob-babel {#自定义-ob-babel}


### <span class="org-todo todo TODO">TODO</span> 自定义 ob-swift {#自定义-ob-swift}

[dotfiles/configuration.org at 71adbedf80d4985a14c674f14c386dc8232da824 · Nich...](https://github.com/NicholasTD07/dotfiles/blob/71adbedf80d4985a14c674f14c386dc8232da824/.spacemacs.d/configuration.org#org-babel-executeswift) 

```swift { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun org-babel-execute:swift (body params)
  "Execute a block of Swift code with org-babel."
  (message "executing Swift source code block")
  (ob-swift--eval body))

(defun ob-swift--eval (body)
  (with-temp-buffer
    (insert body)
    (shell-command-on-region (point-min) (point-max) "swift -" nil 't)
    (buffer-string)))

(provide 'ob-swift)
```

这是一个定义了 \`org-babel-execute:swift\` 函数的 Emacs Lisp 代码段，用于执行 Swift 代码块。下面是代码的注释说明： 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
;; 定义一个名为 `org-babel-execute:swift` 的函数
;; 该函数接受两个参数，`body` 表示代码块中的代码字符串，
;; `params` 包含了代码块的参数列表和其他信息
(defun org-babel-execute:swift (body params)
  ;; 获取参数列表中 `:session` 参数的值，即 REPL 会话名称
  (let ((session (cdr (assoc :session params))))
    ;; 如果 `:session` 参数的值为 "none"，表示不使用 REPL，
    ;; 直接调用 `ob-swift--eval` 函数执行代码
    (if (string= "none" session)
        (ob-swift--eval body)
      ;; 否则使用 `ob-swift--eval-in-repl` 函数在 REPL 中执行代码
      (ob-swift--eval-in-repl session body)))))
```


### <span class="org-todo todo TODO">TODO</span> 自定义AI ob-slack 对话模块 {#自定义ai-ob-slack-对话模块}

新建模块在 emacs-slack 基础上，新增 ob-babel 功能，例如在org-mode 中和指定的人聊天 

```elisp { linenos=true, linenostart=1, hl_lines=["0-0","0-0"] }
(defun org-babel-execute:slack (body params)
  "Execute Slack message BODY with PARAMS."
  (let* ((user (cdr (assoc :user params)))
         (buffer-name (format "*Slack - %s*" user)))
    (if user
        (slack-user-buffer user) ; Enter private buffer with user
      (slack-buffer)) ; Enter default Slack buffer
    (insert body)
    (slack-send-message-or-comment)
    (slack-read-mode)))

(defvar org-babel-default-header-args:slack
  '((:user . "创客屋 : Claude") ; Default Slack user
    (:session . "none")
    (:results . "none"))
  "Default header arguments for slack blocks.")

(add-to-list 'org-babel-exec-map '("slack" . org-babel-execute:slack))
(add-to-list 'org-babel-prep-session-alist '(slack . org-babel-prep-session:slack))
```

```slack
Hi there! How's it going?
```

