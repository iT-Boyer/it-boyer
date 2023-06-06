+++
title = "hugo 中实现支持 uml 的方式"
date = 2019-08-08T20:39:00+08:00
lastmod = 2021-05-08T19:06:53+08:00
tags = ["UML","hugo"]
categories = ["博客站务"]
draft = false
author = "iTBoyer"
+++

1.  使用 [mermaidjs](https://mermaidjs.github.io/#/)  
    引入 js 文件:[mermaid.min.js](<https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js>)  
    
    ```sh
    vi themes/even/layouts/partials/footer.html
    #添加下面代码
    <!-- mermaid JS -->
    <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
    <script>
        mermaid.initialize({ startOnLoad: true });
    </script>
    ```
2.  在 md 文件中添加代码块  
    
    ```sh
    <div class="mermaid">
      graph LR
          A --- B
          B-->C[fa:fa-ban forbidden]
          B-->D(fa:fa-spinner);
    </div>
    ```
