+++
title = "使用swift版XcodeProj读取和修改项目的build配置"
description = "熟悉XcodeProj基本方法的使用,实现通过swift脚本修改build配置"
date = 2021-08-20T18:36:00+08:00
publishDate = 2021-08-20T17:00:00+08:00
lastmod = 2021-08-20T18:36:58+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [目的](#目的)
- [主要的依赖设置](#主要的依赖设置)
- [通过xcodeproj在工程的xxx group下引入xx.h和xx.m文件](#通过xcodeproj在工程的xxx-group下引入xx-dot-h和xx-dot-m文件)
- [通过xcodeproj在工程中引入framwork、.a文件和bundle文件](#通过xcodeproj在工程中引入framwork-dot-a文件和bundle文件)
- [通过xcodeproj在把xx.framework xxx.h xxx.bundle 加入到copy files phase中](#通过xcodeproj在把xx-dot-framework-xxx-dot-h-xxx-dot-bundle-加入到copy-files-phase中)
- [通过xcodeproj设置证书和otherlink](#通过xcodeproj设置证书和otherlink)

</div>
<!--endtoc-->


## 目的 {#目的}

场景  
iOS开发过程中,build setting 相关配置的问题时常发生,后来有了cocoapods减轻了很多重担。但是有些项目还是需要手动配置，实现简单的自动化。这样就要深入了解xcode项目的结构[Monobjc](http://www.monobjc.net/xcode-project-file-format.html) 。针对项目结构的解析工具，最常用的要属 [Xcodeproj (1.21.0)](https://www.rubydoc.info/gems/xcodeproj/Xcodeproj) 比较流行，它基于ruby 语法实现的。今天主要介绍swift版本的[XcodeProj: 📝 Read, update and write your Xcode projects](https://github.com/tuist/XcodeProj#documentation-) 。  

下面主要参考[xcodeproj使用 | 小玉的技术博客](https://blog.devzou.com/2019/07/31/ios/2019-07-31-xcodeproj%E4%BD%BF%E7%94%A8/) 介绍使用swift 版实现常用的几种操作。  


## 主要的依赖设置 {#主要的依赖设置}

通过xocde工具新增两个依赖。  

1.  XcodeProj
2.  PathKit

<!--listend-->

{{< highlight swift "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
import XcodeProj
import PathKit
{{< /highlight >}}


## 通过xcodeproj在工程的xxx group下引入xx.h和xx.m文件 {#通过xcodeproj在工程的xxx-group下引入xx-dot-h和xx-dot-m文件}

1.  先获取project  
    工程中的group 配置在project中，先获取project  
    
    {{< highlight swift "linenos=table, linenostart=1, hl_lines=6-6 0-0" >}}
       let path = Path("/Users/boyer/Desktop/XcodeTest/XcodeTest.xcodeproj") // Your project path
       let xcodeproj = try! XcodeProj(path: path)
       let pbxproj = xcodeproj.pbxproj // Returns a PBXProj
       // 获取到project
       let project = pbxproj.projects.first! // Returns a PBXProject
    {{< /highlight >}}
2.  拿到project, 处理group, 主要通过 `addFile(at:sourceRoot:)` 引入文件。  
    
    {{< highlight swift "linenos=table, linenostart=1, hl_lines=6-7 0-0" >}}
       // 修改目录结构
       let mainGroup = project.mainGroup
       let group = try mainGroup?.addGroup(named: "MyGroup")
       let newgroup:PBXGroup? = group?.last
       newgroup?.children.append(fileee!)
       let filep = Path.init("/Users/boyer/Desktop/test/tet.txt")
       try newgroup?.addFile(at: filep, sourceRoot: Path("/Users/boyer/Desktop/"))
    {{< /highlight >}}
    
    -   at: 指定将要引入的文件绝对路径
    -   sourceRoot: 指定xcode项目路径的父目录。


## 通过xcodeproj在工程中引入framwork、.a文件和bundle文件 {#通过xcodeproj在工程中引入framwork-dot-a文件和bundle文件}

先获取target，在中 target 中方法 `frameworksBuildPhase()` 获取framework属性。  

{{< highlight swift "linenos=table, linenostart=1, hl_lines=11-11 0-0" >}}
pbxproj.nativeTargets.forEach{ target in
    print(target.name)
    //phase
    let obj:PBXFrameworksBuildPhase? = try! target.frameworksBuildPhase()
    //打印当前frame 清单
    obj!.files?.forEach({ filess in
        print("Framework \(filess.file?.name)")
    })
    // 添加新的framework
    let frameworkfile = PBXFileElement(path:"/Users/test/YunCeng.framework",name:"YunCeng.framework")
    let buildfile:PBXBuildFile? = try! obj?.add(file: frameworkfile)
    // 打印加入后的最新清单
    obj!.files?.forEach({ filess in
        print("Framework \(filess.file?.name)")
    })
}
{{< /highlight >}}


## 通过xcodeproj在把xx.framework xxx.h xxx.bundle 加入到copy files phase中 {#通过xcodeproj在把xx-dot-framework-xxx-dot-h-xxx-dot-bundle-加入到copy-files-phase中}


## 通过xcodeproj设置证书和otherlink {#通过xcodeproj设置证书和otherlink}

{{< highlight swift "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
pbxproj.nativeTargets.forEach{ target in
    print(target.name)
    //配置
    target.buildConfigurationList?.buildConfigurations.forEach({ config in
        print("buildconfig == \(config.name)")
        //config.buildSettings 字典类型
        //证书配置配置:
        config.buildSettings["PROVISIONING_PROFILE_SPECIFIER"] = "xxProfileName"
        config.buildSettings["DEVELOPMENT_TEAM"] = "xxTeamName"
        config.buildSettings["CODE_SIGN_IDENTITY"] = "xxIdentityName"
        config.buildSettings["CODE_SIGN_IDENTITY[sdk=iphoneos*]"] = "iPhone Developer"

        //OTHER_LDFLAGS:
        if let kj = config.buildSettings["OTHER_LDFLAGS"]{
            print("other link ld flags \(kj)")
            config.buildSettings["OTHER_LDFLAGS"] = "-fobjc -objc"
        }else{
            print("OTHER_LDFLAGS 不存在..")
            config.buildSettings["OTHER_LDFLAGS"] = "-fobjc -objc"
        }


        //修改配置
        print("===== end =====")
    })
}
{{< /highlight >}}
