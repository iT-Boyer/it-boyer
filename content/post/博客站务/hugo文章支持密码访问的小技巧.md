+++
title = "hugo文章支持密码访问的小技巧"
date = 2019-08-09T14:00:00+08:00
lastmod = 2019-08-09T17:50:15+08:00
tags = ["hugo"]
categories = ["博客站务"]
draft = false
author = "iTBoyer"
#password = 1234
+++

给文章添加访问权限,为自己知识产权多一份保障.
本文章基于even主题,添加文章密码.


## 修改主题even的header.html {#修改主题even的header-dot-html}

{{< highlight sh >}}
vi themes/even/layouts/partials/head.html
{{< /highlight >}}

在head.html底部添加js脚本:

{{< highlight js >}}
 <script>
    (function(){
        if('{{ .Params.password }}'){
            if (prompt('请输入文章密码') !== '{{ .Params.password }}'){
                alert('密码错误！');
                history.back();
            }
        }
    })();
</script>
{{< /highlight >}}


## 配置文章默认密码值 {#配置文章默认密码值}

需要通过对post.org头设置,新增自定义字段`password`

{{< highlight js >}}
#+password:
#+hugo_custom_front_matter: :author "iTBoyer" :password "0000"
{{< /highlight >}}

这样,在org转md之后,会在md文章头部自动添加:

{{< highlight js >}}
...
author = "iTBoyer"
password = 0000
+++
{{< /highlight >}}

运行: `hugo server`
访问该文章就会提示,输入密码弹出框了.
