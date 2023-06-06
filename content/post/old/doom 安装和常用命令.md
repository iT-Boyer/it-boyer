+++
title = "doom 使用"
lastmod = 2020-07-19T21:41:57+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

## 编辑命令 {#编辑命令}

撤销：undo (u)  
重做：C + r :undo-fu-only-redo  
只读：spc t r  
浏览编辑历史树：M-x: undo-tree-visualize(C-x u)  


## 安装和配置 {#安装和配置}


### dotfile 中的配置 {#dotfile-中的配置}


### hugo 项目中管理 doom 版本 {#hugo-项目中管理-doom-版本}


### doom 工具的基本命令（install/refresh/upgrade）使用 {#doom-工具的基本命令-install-refresh-upgrade-使用}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-03-24 二 15:46]</span></span>

\`控制工作\`：任务任用  


#### 升级 doom 和 package {#升级-doom-和-package}

方式一：  

<div class="shell">
  <div></div>

doom upgrade   # or 'doom up'自动升级

</div>

方式二：手动升级  

<div class="shell">
  <div></div>

cd ~/.emacs.d  
git pull        # updates Doom  
doom clean      # Ensure your config isn't byte-compiled  
doom sync       # synchronizes your config with Doom Emacs  
doom update     # updates installed packages

</div>


#### 仅升级 package {#仅升级-package}

To upgrade only your packages (and not Doom itself):  

<div class="shell">
  <div></div>

doom upgrade --packages

</div>


#### 重装 doom 环境 {#重装-doom-环境}

[重装两种方式：](https://github.com/hlissner/doom-emacs/blob/develop/docs/getting%5Fstarted.org#updowngrading-emacs)  

<div class="emacs">
  <div></div>

rm -rf ~/.emacs.d/.local  
doom sync

</div>


### doom 中的 module 创建和实现 helloword 加载实现 {#doom-中的-module-创建和实现-helloword-加载实现}


### doom 帮助文档的使用 {#doom-帮助文档的使用}


## 统计命令 {#统计命令}

org-insert-columns-dblock C-c C-x i 对任务列表生成列表视图.C-c C-x C-c 生成临时的任务列表  
org-clock-in  C-x C-x C-i 统计实际任务执行时间  
org-clock-out C-c C-x C-o 统计任务结束的总时长  
org-clock-report C-c C-x C-r 统计光标的任务时间报告  


## 插入命令 {#插入命令}

插入代码片段:  
C-c C-q 标签命令  
C-c i s 插入脚本  
, i h 插入同级节点  
C-c C-w org 文件之间迁移节点  
, i p 添加 org 属性  


## 目录命令 {#目录命令}


### 进入 Dired 模式：spc f d {#进入-dired-模式-spc-f-d}


### 启动 treemacs 模式：spc o p {#启动-treemacs-模式-spc-o-p}


## 搜索 {#搜索}


### 文件名搜索 {#文件名搜索}

find-dired  
find-grep-dired 递归所有子目录  
grep 在当前目录中查找指定的 regexp  


### 文本搜索替换 {#文本搜索替换}

grep-find 显示包含字段的行，递归子目录  


## agenda 命令 {#agenda-命令}


### 编辑 tag(提炼任务，矩阵化) {#编辑-tag--提炼任务-矩阵化}

在 buffer 中：spc + m + q  
在 agenda 中：c t  


### 过滤任务 {#过滤任务}

在 agenda 视图中：s 筛选任务  


#### 标签过滤 {#标签过滤}


#### 进度过滤 {#进度过滤}


### 安排行程 {#安排行程}

spc m s :org-schedule 设置日期在 agenda 中：t 打开状态视图在 org 文件中：shift + 左右键  


### 跟进进度 {#跟进进度}

在 agenda 中：a 给任务添加备注信息在 agenda 中：c T 设置番茄钟倒计时  


## 导出 markdown {#导出-markdown}

spc + m + e 导出窗口  
spc + m + o 设置条目属性：EXPORT\_FILE\_NAME  
<http://holbrook.github.io/2012/04/12/emacs%5Forgmode%5Feditor.html>  
文档元数据  
\##+OPTIONS 是复合的选项：支持在文档头部定义，也可以在条目下:PROPERTIES:属性中定义。  

1.  **todo:** off:不显示任务条目的状态符号（TODO，DONE）等。
2.  **tasks:** nil:移除所有的任务项 todo:保留 todo 状态任务 done:保留 done 状态任务不设置时，显示所有任务项
3.  **\n:** t:开启，自动换行
4.  **<:** 时间戳
5.  


## tree 结构 {#tree-结构}


### 升/将级节点 {#升-将级节点}

option + 左右键（升/将）编辑状态下，Tab 当前节点  


### 定位/展开/关闭当前节点 {#定位-展开-关闭当前节点}

定位：C+x n s org-narrow-to-subtree  
定位 buffer：spc b -  
展开：z a org/taggle-fold  
关闭：z c org/close-fold  
关闭父节点：shift + tab  


### 新增节点 {#新增节点}


#### 同级节点: org-insert-heading {#同级节点-org-insert-heading}

热键：option + 回车终端：C+c return  


#### 子节点: org-insert-subheading {#子节点-org-insert-subheading}

option + command + 回车  


### 新增 TODO 节点 {#新增-todo-节点}

org-insert-todo-heading    : 同级 todo  
org-insert-todo-subheading : 子节点 todo  


### 新增 checkbox {#新增-checkbox}

markdown-insert-gfm-checkbox  


## eww 浏览器 {#eww-浏览器}


### 配置默认搜索引擎 {#配置默认搜索引擎}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-05-06 三 16:15]</span></span>

在 config 中配置：默认搜索引擎  
(setq eww-search-prefix "<https://github.com/search?q>=")  


### 常用命令：M-x: eww {#常用命令-m-x-eww}


#### 书签： {#书签}

1.  收藏：m  
    收藏列表：g b
2.  历史记录：g h


#### 修改路径 {#修改路径}

o:打开地址栏  
H:返回上一页  


### 打开文本链接 Link {#打开文本链接-link}

M-x: org-agenda-open-link:在系统浏览器中打开 org 中的超文本链接  


## 表格编辑 {#表格编辑}

新建：M-x table-insert 或 org-table-create  
编辑：M-x org-edit-speical (spc  m  ')  


## 脚本运行命令 {#脚本运行命令}

C-c C-c: 执行命令,光标必须定位在 BEGIN\_SRC 模块  


## emacs 配置命令 {#emacs-配置命令}

SPC f e d 打开配置文件  
SPC f e R 重加载配置文件  
Shift h 查看当前 buffer 启动的 mode  

我最近开始使用 emacs 。Evil 和 org 模式。 当在组织模式下记录时，程序会进入 <E> 模式，我可以在框架底部的状态栏上看到。 启动 emacs 时，我通常在状态栏上看到一个 <N> 。我认为  
<N> - 用于正常模式  
<I> - 用于插入模式什么是 <E> 模式？如何在不重新启动 emacs 的情况下返回到正常模式？  
[Emacs Evil <E> 模式和org模式\_emacs\_HELPLIB 编程知识库](https://ask.helplib.com/emacs/post%5F12452808)  


### yasnippet 使用 {#yasnippet-使用}

暂时把所有的 snippet 都移动到 org-mode 中，可以正常加载使用  

{{< highlight nil >}}
mv plantuml-mode org-mode
{{< /highlight >}}

仅支持在 org 文件内，使用\`Tab\`展开。  


### magit 版本控制命令 {#magit-版本控制命令}

spc g s 进入 status 窗口  
   ? 进入 git 帮助窗口  
   c commit 输入提交信息  
   C-c C-c 完成提交  
   C-c C-k 取消提交操作  
   l 查看 git 日志  
   z stash 命令  
   F pull 命令  
   ' submodule 命令  
   " subtree 命令  


## 在 treemacs 中无法启动 file-templates 问题 {#在-treemacs-中无法启动-file-templates-问题}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-05-11 一 09:35]</span></span>

在 ~/.doom.d/config.el 加入如下脚本：  

<div class="elisp">
  <div></div>

(add-hook! 'treemacs-create-file-functions  
  (defun expand-file-template (file)  
    (with-current-buffer (or (get-file-buffer file)  
                             (find-file-noselect file))  
      (when-let (rule (cl-find-if #'+file-template-p +file-templates-alist))  
        (apply #'+file-templates--expand rule)))))

</div>
