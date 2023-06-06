+++
title = "plantuml 使用"
lastmod = 2020-08-30T23:02:53+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

下载历史 jar包[plantuml.jar历史版本路径](https://sourceforge.net/projects/plantuml/files/)  


## 命令行 uml 预览 {#命令行-uml-预览}

1.  您也可以使用下面的命令行运行 PlantUML:  
    java -jar plantuml.jar file1 file2 file3  
    这将在 file1, file2 和 file3 中寻找 @startXYZ。 对于每个图表, 都将创建一个 .png 文件。
2.  对于处理整个文件夹, 您可以使用:  
    java -jar plantuml.jar "c:/directory1" "c:/directory2"  
    此命令将寻找 @startXYZ 和 @endXYZ 位于 c:/directory1 和 c:/directory2 目录中的 .c, .h, .cpp, .txt, .pu, .tex, .html, .htm 或.java 文件。
3.  辅助方法在 dotfiles/doom/aliases.zsh 中添加别名。  
    alias plantuml='java -jar ~/.emacs.d/.local/etc/plantuml.jar'


## M-x: plantuml-download-jar {#m-x-plantuml-download-jar}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-04-10 Fri 17:31]</span></span>

当缺少 jar 时，使用命令该命令安装 jar  


## 安装 graphviz@28.6 {#安装-graphviz-28-dot-6}

-   State "CANCEL"     from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-04-20 Mon 13:41] </span></span> <br />
    暂时不知道怎么安装旧版本

需要安装老版本，官方提示：2.40.1 版本存在问题，目前无法找到旧版的安装方法  

<div class="shell">
  <div></div>

brew install libtool  
brew link libtool  
brew install graphviz  
brew link --overwrite graphviz

</div>

[graphviz安装说明](https://plantuml.com/zh/graphviz-dot)  


## plantuml 安装第三方服务器 {#plantuml-安装第三方服务器}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-04-20 Mon 13:42]</span></span>
-   [官网服务器](https://github.com/plantuml/plantuml-server)  
    
    <div class="shell">
      <div></div>
    
    docker run -d -p 8080:8080 plantuml/plantuml-server:jetty  
    docker run -d -p 8080:8080 plantuml/plantuml-server:tomcat
    
    </div>
-   [在线预览韩国版](https://github.com/iT-Boyer/plantuml-editor)  
    
    <div class="shell">
      <div></div>
    
    npm install  
    
    npm run flow-typed  
    
    npm run serve  
    
    npm run build  
    
    npm run test:unit  
    
    npm run test:e2e
    
    </div>
    
    1.  问题问题 1：npm run flow-typed 执行命令失败问题 2: 访问<http://localhost:8080> 提示如下：  
        
        <div class="log">
          <div></div>
        
        Failed to compile.  
        
        ./src/components/UmlSvg.vue  
        Module Error (from ./node\_modules/eslint-loader/index.js):  
        
        /Users/jhmac/hsg/plantuml-editor/src/components/UmlSvg.vue  
          7:19  error  Cannot resolve module `axios`  flowtype-errors/show-errors  
        
        ✖ 1 problem (1 error, 0 warnings)
        
        </div>

-   [haha98k](https://github.com/iT-Boyer/haha98k/)  
    
    <div class="shell">
      <div></div>
    
    $ git clone <https://github.com/iT-Boyer/haha98k.git>  
    $ cd web  
    
    $ yarn install  
    
    $ yarn run dev  
    
    $ yarn run build  
    $ yarn start  
    
    $ yarn run generate
    
    </div>


## ProOne 电脑上 plantuml-mode 失效问题 {#proone-电脑上-plantuml-mode-失效问题}

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-04-10 Fri 17:31]</span></span>

[plantuml-mode丢失问题](https://github.com/hlissner/doom-emacs/issues/2864)  
解决办法：~/.emacs.d/.local/straight/repos/plantuml-mode  

<div class="shell">
  <div></div>

git reset HEAD .  
git checkout .  
doom sync  
doom refresh

</div>
