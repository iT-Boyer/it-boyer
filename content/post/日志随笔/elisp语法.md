---
title: "在elisp 中如何向list 变量添加新元素"
description: "博客简介"
date: 2025-12-10T22:27:00+08:00
lastmod: 2025-12-10T22:27:41+08:00
tags: ["AI"]
categories: ["Inbox"]
draft: false
authors: ["iTBoyer"]
cover: "https://mmbiz.qpic.cn/mmbiz_jpg/v5Lr5bLDWhxyvgAR84nTjovnGSuhiaicCibJLibL317icK6kkhLpo5YKzqUU8libC3CpwWmBCgwhscfF48W3okaB3c6A/640?wx_fmt=jpeg"
---

<style>
.org-todo {
    font-size: 0.8em;
    font-weight: 700;
}
/* *** Org TODO set to TODO state */
.org-todo.todo {
    color: #e60000;
}
/* *** Org TODO set to DONE state */
.org-todo.done {
    color: green;
}

.org-todo.cancel {
    color: blue;
}
</style>

**1. `push` —— 最方便的头部添加** 

-   用法：=(push new-element list-variable)=
-   效果：把新元素加到列表最前面，直接修改原变量
-   文档：[GNU Emacs Lisp手册 - 修改列表](<https://www.gnu.org/software/emacs/manual/html_node/elisp/Modifying-Lists.html>)

<!--listend-->

```elisp
(setq my-list '(a b c))
(push 'x my-list)
my-list  ; => (x a b c)
```

**2. `add-to-list` —— 防止重复的添加** 

-   用法：=(add-to-list 'list-variable new-element)=
-   特点：如果元素已经存在就不添加，适合用来维护唯一值列表
-   文档：[GNU Emacs Lisp手册 - 列表变量](<https://www.gnu.org/software/emacs/manual/html_node/elisp/List-Variables.html>)

<!--listend-->

```elisp
(setq colors '(red green blue))
(add-to-list 'colors 'yellow)  ; 添加
(add-to-list 'colors 'red)     ; 这个不会重复添加
```

**3. `append` —— 连接列表（不修改原变量）** 

-   用法：=(append list1 list2)=
-   注意：这个返回新列表，不改变原变量，通常要和=setq=配合使用

<!--listend-->

```elisp
(setq list1 '(a b))
(setq list1 (append list1 '(c)))  ; => (a b c)
```

**4. `nconc` —— 破坏性连接** 

-   用法：=(nconc list1 list2)=
-   警告：会直接修改第一个列表的结构，除非你知道在干什么，否则少用

总结一下： 

-   想往前面加用 `push`
-   要避免重复用 `add-to-list`
-   普通追加用 `append` + `setq`
-   `nconc` 有副作用，慎用

这些方法在Emacs自带的=\*scratch\*=缓冲区里都能直接测试，建议你亲手试试看。 

