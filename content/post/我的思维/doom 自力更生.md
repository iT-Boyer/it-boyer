+++
title = "Doom自力更生"
description = "掌握常用的技巧和方法"
date = 2021-08-30T17:20:00+08:00
lastmod = 2021-08-31T10:03:22+08:00
categories = ["日志随笔"]
draft = false
authors = ["iTBoyer"]
+++

## 目的 {#目的}

在使命宣言中已经确定使用 emacs 作为唯一官方的时间管理工具，以后在生活中，会遇到其他效率问题，激发出各种提高效能的灵感和想法，如果能掌握 emacs 开发，及时的把灵感变为实用的工具，通过工具（技巧）来改变习惯，掌握了 emacs 开发，这将会是潜在的高效产能。  

通过工具来改变习惯的方式，将会特别有效，学习使用 el 语言和 doom 的设计模式  

确定了期望，如果想要想掌握一门语言需要实用的开发环境和好用的框架来支撑，才能在灵感窍门时，能够抓住机会，快速转换 emacs 思维，通过开发一套实用的工具，来改造自己的习惯。  

环境：mac 环境下，emacs 框架：doom 框架下开发  

入门：在 doom 下读文档的习惯  

第一步：熟悉在 doom 中实现基本的语法开发环境  

第二步：doom 加载 helloworld 方法，并实用  

在 org-mode 下编写 el 脚本，并运行：spc c e 或 C-x C-e 或 spc ;  

给方法添加注释: 使用双引号实现注释  

支持和 M-x 交互：(interactive)  

掌握基础语法：变量，数组，函数，循环，正则，字符串查找，清空 buffer  


## 进入文档：spc h d h {#进入文档-spc-h-d-h}

1.  package 管理器学习 doom 中 Package 管理器的机制  
    
    安装方式的介绍：可以自定义安装库的路径，支持安装本地 Package，支持指定 git 提交版本。  
    
    还有 package 配置相关，可以在指定时机设置属性，可以指定加载时机，可以在配置中禁用包。  
    
    还有目前重要的：可以在 doom 中联调 el 脚本
2.  Doom 相关配置 `Configuring packages` :提供了 package 的下钩子的方法。 `Reloading your config` :提供了在 doom 下调试 el 表达式的方法。
3.  创建自己的模块 `Writing your own modules` 创建模块（即新建目录）：~/.doom.d/modules/abc/xyz  
    
    激活模块（init.el 配置）：(doom! ... :abc xyz)  
    
    覆盖内置模块：在 modules 下，新建内置模块同名模块即可  
    
    1.  模块的目录结构和文件功能介绍 `File structure`  
        
        模块加载的配置：~=config.el=~  
        
        模块依赖包关系：~=packages.el=~  
        
        实现通用的工具方法: `=autoload/*.el=` OR `=autoload.el=`  
        
        目前测试在两个文件声明的方法：  
        
        1.  helloworld 在文件 boyer/helloworld/config.el
        2.  helloworld\_autoload 在文件 boyer/helloworld/autoload.el  
            1.  添加 autoload 注释
            2.  添加方法注释的方法：在 defun 第二行使用双引号在第二行实现注释：双引号""  
                
                ```elisp
                                         (defun helloWorld_autoload ()
                                           "在doom中创建的第一个方法，学习el基础语法，和doom模块的加载机制的学习，实现doom交互"
                                           (interactive)
                                           (message "我是一个支持交互的方法，请多多关照")
                                           (switch-to-buffer-other-window "helloWorld_autoload")
                                           (erase-buffer)
                                           (insert "加载我的方法")
                                           (other-window 1)
                                           )
                ```
            3.  支持 M-x 交互：(interactive)
        3.  helloworld\_test 在 boyer/helloworld/autoload.el 主要验证不添加;;;#autoload 注释，也可以加载成功
    2.  验证 helloworld 模块是否安装成功  
        1.  重启 emacs
        2.  查找函数：spc h f helloworld
        3.  交互函数：M-x:helloworld
4.  加速 doom sync 方法 <https://github.com.cnpmjs.org> <https://hub.fastgit.org>  
    1.  替换阿里代理  
        
        ```shell
                    git config --global url.https://github.com.cnpmjs.org/.insteadof https://github.com/
        ```
        
        [doom emacs 用户的福音 - Emacs-general - Emacs China](https://emacs-china.org/t/doom-emacs/16069)
    
    2.  在 init.el 中添加  
        
        ```sh
                    'emacs-china镜像
                    (setq package-archives '(("gnu" . "http://elpa.emacs-china.org/gnu/")
                                             ("org" . "http://elpa.emacs-china.org/org/")
                                             ("melpa" . "http://elpa.emacs-china.org/melpa/")))
        
                    '清华镜像
                    (setq package-archives '(("gnu" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/gnu/")
                                             ("org" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/org/")
                                             ("melpa" . "http://mirrors.tuna.tsinghua.edu.cn/elpa/melpa/")))
        ```


## 在 doom 框架下创建自己的 helloword-mode<code>[6/6]</code> {#在-doom-框架下创建自己的-helloword-mode}

-   State "DONE"       from              <span class="timestamp-wrapper"><span class="timestamp">&lt;2021-04-09 周五 07:25&gt;</span></span>

<!--listend-->

-   [X] 在 emacs 读 doom 文档实现加载过程
-   [X] 在 helloword 模块中添加方法，doom 加载之后并使用
-   [X] 在 doom 中开发模块： boyer/helloworld


## 编写旅游团小程序，学习 el 基础语法 {#编写旅游团小程序-学习-el-基础语法}

-   [X] 编写一个欢迎旅游团的小程序，在荧光屏打印欢迎语  
    1.  组织旅游团数组声明，方法声明，方法调用使用 seq 创建一个观光团：team  
        
        ```elisp
                   ;;创建团队初始团队三人
                   (setq teams '("张三" "里斯" "万二"))
                   ;;选一个队长
                   (setq leader (car teams))
                   ;;指定其他队友
                   (setq other (cdr teams))
                   ;;加入两个新成员
                   (push "于三" teams)
                   ;;在点名方法
                   (defun call (name)
                     (insert (format "点名: %s !\n" name))
                     )
                   (mapcar 'call teams)
        ```
    2.  在车上欢迎队友窗口切换，擦除 buffer，切换到当前页面  
        
        ```elisp
                 (setq car "专121公交台")
                 (defun greet ()
                   (switch-to-buffer-other-window car)
                   (erase-buffer)
                   (mapcar 'call teams)
                   (other-window 1)
                 )
                 (greet)
        ```
-   [X] 欢迎语临时变更，更新荧光屏上欢迎语字符串搜索，报错问题  
    
    ```elisp
            (defun replace_call_greet ()
              (switch-to-buffer-other-window car)
              (goto-char (point-min))
              ;; `nil' 参数的意思是 : 查找并不限于某个范围内
              ;; `t' 参数的意思是: 当什么都没找到时，不给出错误提示
              (while (search-forward "点名" nil t)
                     (replace-match "欢迎"))
              (other-window 1)
              )
            (replace_call_greet)
    ```
-   [X] 在荧屏上加粗队员名称正则的使用方法更新字体的方法  
    
    ```elisp
         (defun bold_name ()
              (switch-to-buffer-other-window car)
              (goto-char (point-min))
              (while (re-search-forward "欢迎：\\(.+\\)!" nil t)
                (add-text-properties (match-beginning 1)
                                     (match-end 1)
                                     (list 'face 'bold)))
              (other-window 1)
              )
      (bold_name)
    ```


## doom/reload 刷新问题 {#doom-reload-刷新问题}

1.  当遇到方法找不到的问题，先找到 mode 的源码库名，进入~/.emacs.d/.local/straight/repos/mode 库名。重新拉去代码，在执行编译命令 doom sync。  
    
    例如： `compilation-buffer-name: Symbol’s function definition is void: projectile-compilation-buffer-name`

2.  当遇到环境问题，例如:无法找到 emacs 的提示，此时考虑环境变量，iterm 环境和 doom 环境是不同的，doom 环境配置需要执行 doom env 生成 doom 的环境变量。  
    
    {{< highlight sh "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
       "/Users/venkobas/doom-emacs/bin/doom" sync -e
       Can't find emacs in your PATH
       Comint exited abnormally with code 1 at Sun Jan 17 00:59:49
    {{< /highlight >}}
