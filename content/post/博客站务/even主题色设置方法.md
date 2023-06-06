+++
title = "设置even主题色"
description = "通过自定义设置，让even主题色更加丰富"
date = 2021-10-14T11:50:00+08:00
publishDate = 2021-10-14T11:12:00+08:00
lastmod = 2021-10-14T11:50:08+08:00
categories = ["博客站务"]
draft = false
authors = ["iTBoyer"]
+++

## 设置主题色 {#设置主题色}

主题的样式文件的位置： `${Hugo-Site}/themes/even/assets/sass/_variables.scss`  

1.  先确定配色方案  
    
    背景色： `#002b36`  
    
    字体色： `#839496`  
    
    可以在 [Solarized Color Scheme](https://ethanschoonover.com/solarized/#features) 找到更多的配色方案。  
    
    下面步骤将用这个色值设置even 主题色，本博客就是最终效果。

2.  设置背景色  
    
    在 `_variables.scss` 中找到 `$global-background` 属性值为 `$white`  
    
    {{< highlight css "linenos=table, linenostart=1, hl_lines=2-2 0-0" >}}
       // Background color of the site.
       $global-background: $white !default;
    {{< /highlight >}}
3.  修改 `$white` 色值  
    
    修改为 `#002b36` 色号  
    
    {{< highlight css "linenos=table, linenostart=1, hl_lines=3-3 0-0" >}}
       // ========== Color ========== //
       $black: #0a0a0a !default;
       $white: #002b36 !default; //#fefefe
       $light-gray: #e6e6e6 !default;
       $gray: #cacaca !default;
       $dark-gray: #8a8a8a !default;
    {{< /highlight >}}
4.  设置字体的全局色  
    
    Global 下 $global-font-color 的sRGB 值  
    
    {{< highlight css "linenos=table, linenostart=1, hl_lines=3-3 0-0" >}}
       // ========== Global ========== //
       // Text color of the body. #34495e
       $global-font-color: #839496 !default;
    {{< /highlight >}}
5.  其他  
    
    在完成上两步之后，可以看到效果样式。还有toc 样式背景需要做统一色设置。  
    
    toc 浮动框背景色  
    
    {{< highlight css "linenos=table, linenostart=1, hl_lines=5-5 0-0" >}}
       // ========== TOC ========== //
       // Width of the post toc.
       $post-toc-width: 260px !default;
    
       // Backgroud color of the post toc. rgba($deputy-color, 0.6)
       $post-toc-backgroud: rgba($white, 1) !default;
    {{< /highlight >}}

参考： [更新 hugo-even 主题样式 - Herbert's Blog](https://blog.herbert.top/2020/07/09/how%5Fchange%5Fhugo%5Feven%5Ffont/#solarized-dark)
