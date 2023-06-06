---
title: 使用Cocoapods创建私有podspec
date: 2017-02-28 15:46:16
updated: 2019-01-16 21:01:14
comments: true
tags: [pod]
categories:
- 学习笔记
keywords: 
permalink:
---

## 创建一个版本库来托管pod索引:pod repo add命令 
第一步：在github登录个人账号，创建一个库作为pod索引托管库。也可根据具体情况可以选择：`github`、`CODING`、`开源中国`、`Bitbucket`以及`CSDN`等，代码托管平台。
当索引库托管在github私有的仓库时，如果有其他同事共同开发维护，则需要配置Git仓库的权限。
第二步：pod化版本库，使其专门用户管理个人的`*podspec`索引文件
{% codeblock repo add命令 lang:ruby %}
# pod repo add [Private Repo Name] [GitHub HTTPS clone URL]
$ pod repo add PodRepo https://github.com/it-boyer/PodRepo.git
#输出：
> Cloning spec repo `PodRepo` from `https://github.com/it-boyer/PodRepo.git`
{% endcodeblock %}
此时在本地会生成`~/.cocoapods/repos/PodRepo`目录，这个目录就可以用来存储你所有的开源包。
每当一个或多个`*.podspec`文件测试无误时，就可以使用`pod repo push`命令向私有索引库中提交该文件。
{% codeblock lib create命令 lang:ruby %}
$ pod repo push PodRepo LogSwift.podspec  #PodRepo是本地Repo名字 后面是podspec名字
{% endcodeblock %}
完成之后这个组件库就添加到私有索引库中，自动生成的`pod 库`标准结构：

同时`push命令`会将新增的索引目录推送至自己的远程仓库中，即索引托管库`PodRepo.git`中。

下面详述`podspec文件`的配置，检测，使用的过程。

## 使用模板命令创建Pod工程项目 lib create
初始化Pod模板项目：
{% codeblock lib create命令 lang:ruby %}
pod lib create LogSwift
{% endcodeblock %}
有以下五步命令行交互：
{% codeblock 交互 lang:ruby %}
What is your email?
> 
What language do you want to use?? [ Swift / ObjC ]
>Swift
Would you like to include a demo application with your library? [ Yes / No ]
>Yes
Which testing frameworks will you use? [ Quick / None ]
>Quick
Would you like to do view based testing? [ Yes / No ]
>
{% endcodeblock %}
会自动执行`pod install`命令创建项目并生成依赖。

### 添加库文件和资源
例如：把一个网络模块的共有组件放入`Pod/Classes`中，然后进入`Example`文件夹执行`pod update`命令，再打开项目工程可以看到，刚刚添加的组件已经在`Pods`子工程下`Development Pods/PodTestLibrary`中了，然后编辑demo工程，测试组件。
测试无误后需要将该项目添加并推送到远端仓库，并编辑podspec文件。

### 配置podspec文件及验证命令lib lint

#### 打tag号作为podspec版本号
因为`podspec文件`中获取`Git版本控制`的项目还需要`tag号`，所以我们要打上一个`tag`
{% codeblock 打标签 lang:ruby %}
$ git tag -m "first release" 0.1.0
$ git push --tags     #推送tag到远端仓库
{% endcodeblock %}

#### 编辑podspec文件
`podspec文件`是一个Ruby格式：
{% codeblock podspec文件 lang:ruby %}
Pod::Spec.new do |s|
s.name             = "PodTestLibrary"    #名称
s.version          = "0.1.0"             #版本号
s.summary          = "Just Testing."     #简短介绍，下面是详细介绍
s.description      = <<-DESC
Testing Private Podspec.

* Markdown format.
* Don't worry about the indent, we strip it!
DESC
s.homepage         = "https://coding.net/u/boyers/p/podTestLibrary"                           #主页,这里要填写可以访问到的地址，不然验证不通过
# s.screenshots     = "www.example.com/screenshots_1", "www.example.com/screenshots_2"           #截图
s.license          = 'MIT'              #开源协议
s.author           = { "boyers" => "boyers@foxmail.com" }  #作者信息
s.source           = { :git => "https://coding.net/boyers/podTestLibrary.git", :tag => "0.1.0" }      #项目地址，这里不支持ssh的地址，验证不通过，只支持HTTP和HTTPS，最好使用HTTPS
# s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'                       #多媒体介绍地址

s.platform     = :ios, '7.0'            #支持的平台及版本
s.requires_arc = true                   #是否使用ARC，如果指定具体文件，则具体的问题使用ARC

s.source_files = 'Pod/Classes/**/*'     #代码源文件地址，**/*表示Classes目录及其子目录下所有文件，如果有多个目录下则用逗号分开，如果需要在项目中分组显示，这里也要做相应的设置
s.resource_bundles = {
'PodTestLibrary' => ['Pod/Assets/*.png']
}                                       #资源文件地址

s.public_header_files = 'Pod/Classes/**/*.h'   #公开头文件地址
s.frameworks = 'UIKit'                  #所需的framework，多个用逗号隔开
s.dependency 'AFNetworking', '~> 2.3'   #依赖关系，该项目所依赖的其他库，如果有多个需要填写多个s.dependency
end
{% endcodeblock %}

#### 验证podspec文件可用性
{% codeblock  lang:ruby  %}
$ pod lib lint

输出：
-> PodTestLibrary (0.1.0)
PodTestLibrary passed validation.
{% endcodeblock %}
说明验证通过了，不过这只是这个`podspec文件`是合格的，不一定说明这个Pod是可以用的，Pod需要在本地做一下验证。

### 将源码托管到远程仓库
通过`Cocoapods`创建出来的目录本身就在本地的`Git`管理下，我们需要做的就是给它添加远端仓库，同样去`GitHub`或其他的Git服务提供商那里创建一个`私有的仓库`，拿到`SSH地址`，然后`cd`到`LogSwift`目录
{% codeblock 添加到远程仓库 lang:ruby %}
$ git add .
$ git commit -s -m "Initial Commit of Library"
$ git remote add origin https://coding.net/git/LogSwift.git   #添加远端仓库
$ git push origin master        #提交到远端仓库
{% endcodeblock %}

## 手动配置Pod私库项目支持：spec create
{% codeblock lang:ruby %}
$ pod spec create LogSwift git@coding.net:boyers/LogSwift.git
{% endcodeblock %}
执行完之后，就创建了一个`podspec文件`，他其中会包含很多内容，可以按照我之前介绍的进行编辑，没用的删掉。编辑完成之后使用验证命令`pod lib lint`验证一下。

## :path => 在新项目配置检测podspec文件
在这个项目的`Podfile`文件中直接指定刚才创建编辑好的`podspec文件`，看是否可用。
1. 指定本地依赖的两种方式:
{% codeblock Podfile lang:ruby %}
platform :ios, '7.0'
pod 'LogSwift', :path => '~/code/Cocoapods/podTest/LogSwift'      # 指定路径
pod 'LogSwift', :podspec => '~/code/Cocoapods/podTest/LogSwift/LogSwift.podspec'  # 指定podspec文件
{% endcodeblock %}
2. 指定源码的远程仓库作为依赖
前提时索引文件中指定的tag版本的源码必须推送到远程仓库
{% codeblock Podfile lang:ruby %}
platform :ios, '7.0'
pod 'MusicLrc', :git => 'https://github.com/it-boyer/MusicLrcTest.git'
{% endcodeblock %}

然后执行`pod install`命令安装依赖，打开项目工程，可以看到`库文件`和`资源`都被加载到`Pods子项目`中了，不过它们并没有在`Pods目录`下，而是跟测试项目一样存在于`Development Pods/LogSwift`中，这是因为我们是在本地测试，而没有把`podspec文件`添加到`Spec Repo`中的缘故。

## 万事具备，向私有索引库中提交podspec文件：repo push 
每当一个或多个`*.podspec`文件测试无误时，就可以使用`pod repo push`命令向私有索引库中提交该文件。
{% codeblock lib create命令 lang:ruby %}
$ pod repo push PodRepo LogSwift.podspec  #PodRepo是本地Repo名字 后面是podspec名字
{% endcodeblock %}
完成之后这个组件库就添加到私有索引库中，自动生成的`pod 库`标准结构：

同时`push命令`会将新增的索引目录推送至自己的远程仓库中，即索引托管库`PodRepo.git`中。

## trunk push 添加到Cocoapods的官方索引库
### 注册trunk，邮箱验证
在注册trunk之前，我们需要确认当前的CocoaPods版本是否足够新:
sudo gem install cocoapods
开始注册trunk：
{% codeblock lang:bash %}
pod trunk register boyer@163.com 'boyers1250'  --verbose
{% endcodeblock %}
`-verbose`参数是为了便于输出注册过程中的调试信息。
执行上面的语句后，你的邮箱将会受到一封带有验证链接的邮件，如果没有请去垃圾箱找找，有可能被屏蔽了。点击邮件的链接就完成了trunk注册流程。
使用下面的命令可以向trunk服务器查询自己的注册信息：
{% codeblock lang:bash %}
pod trunk me
{% endcodeblock %}

### 通过trunk推送podspec文件
现在我们已经有了自己的podspec文件，但是在推送podspec文件之前你需要确认以下几点：
1. 确保你的源码已经push到Github上。
2. 确保你所push的代码已经打上"version tag"版本号标签：
只有确保了以上两点，CocoaPods才能更准确地找到你的repo。
现在我们开始通过trunk上传你的podspec文件。先cd到podspec文件所在目录，执行：
{% codeblock lang:bash %}
pod trunk push WZLBadge.podspec
{% endcodeblock %}
执行上面的push操作，就相当于你把你的源代码提交给CocoaPods团队审核了，CocoaPods审核只需要几秒钟或者几分钟就可以完成。

##  使用远程的私有Pod库
我们的这个组件库就已经制作添加完成了，现在可以`pod search`命令查到这个库，当使用时配置Podfile依赖文件即可。
1. pod search 查找库
{% codeblock 查找库 lang:ruby %}
$ pod search PodTestLibrary

-> PodTestLibrary (0.1.0)
Just Testing.
pod 'PodTestLibrary', '~> 0.1.0'
- Homepage: https://coding.net/u/boyers/p/podTestLibrary
- Source:   https://coding.net/boyers/podTestLibrary.git
- Versions: 0.1.0 [WTSpecs repo]
{% endcodeblock %}
2. 在Podfile文件中配置库依赖
{% codeblock 配置库依赖 lang:ruby %}
pod 'PodTestLibrary', '~> 0.1.0'
{% endcodeblock %}

## 更新维护podspec文件配置，升级库版本
`subspec`特性，可以在库原有基础上，添加更多的模块，相应创建了多个子目录。现在尝试添加包括`工具类`，底层`Model`及`UIKit`扩展等。

### 添加模块库文件和资源
具体做法是先将源文件添加到`Pod/Classes`中，然后按照不同的模块对文件目录进行整理，因为我有四个模块，所以在`Pod/Classes`下有创建了四个子目录

### 打tag号作为podspec版本号
因为`podspec文件`中获取`Git版本控制`的项目还需要`tag号`，所以我们要打上一个`tag`
{% codeblock 打标签 lang:ruby %}
$ git tag -m "first release" 0.1.0
$ git push --tags     #推送tag到远端仓库
{% endcodeblock %}

### 更新podspec配置文件 
当创建了`subspec`，之前项目整体的依赖`dependency`:
1. 源文件:`source_files`
2. 头文件:`public_header_files`
3. 资源文件:`resource`
都移动到了各自的`subspec`中，每个`subspec`之间也可以有相互的依赖关系，比如`UIKitAdditio`n就依赖于`CommonTools`。
{% codeblock 更新podspec配置文件  lang:ruby %}
Pod::Spec.new do |s|
s.name             = "PodTestLibrary"
s.version          = "1.0.0"
s.summary          = "Just Testing."
s.description      = <<-DESC
                         Testing Private Podspec.

                        * Markdown format.
                        * Don't worry about the indent, we strip it!
                    DESC
s.homepage         = "https://coding.net/u/boyers/p/podTestLibrary"
# s.screenshots     = "www.example.com/screenshots_1", "www.example.com/screenshots_2"
s.license          = 'MIT'
s.author           = { "boyers" => "boyers@foxmail.com" }
s.source           = { :git => "https://coding.net/boyers/podTestLibrary.git", :tag => "1.0.0" }
# s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'

s.platform     = :ios, '7.0'
s.requires_arc = true

#s.source_files = 'Pod/Classes/**/*'
#s.resource_bundles = { 'PodTestLibrary' => ['Pod/Assets/*.png'] }
#s.public_header_files = 'Pod/Classes/**/*.h'

s.subspec 'NetWorkEngine' do |networkEngine|
    networkEngine.source_files = 'Pod/Classes/NetworkEngine/**/*'
    networkEngine.public_header_files = 'Pod/Classes/NetworkEngine/**/*.h'
    networkEngine.dependency 'AFNetworking', '~> 2.3'
end

s.subspec 'DataModel' do |dataModel|
    dataModel.source_files = 'Pod/Classes/DataModel/**/*'
    dataModel.public_header_files = 'Pod/Classes/DataModel/**/*.h'
end

s.subspec 'CommonTools' do |commonTools|
    commonTools.source_files = 'Pod/Classes/CommonTools/**/*'
    commonTools.public_header_files = 'Pod/Classes/CommonTools/**/*.h'
    commonTools.dependency 'OpenUDID', '~> 1.0.0'
end

s.subspec 'UIKitAddition' do |ui|
    ui.source_files = 'Pod/Classes/UIKitAddition/**/*'
    ui.public_header_files = 'Pod/Classes/UIKitAddition/**/*.h'
    ui.resource = "Pod/Assets/MLSUIKitResource.bundle"
    ui.dependency 'PodTestLibrary/CommonTools'
end

s.frameworks = 'UIKit'
    #s.dependency 'AFNetworking', '~> 2.3'
    #s.dependency 'OpenUDID', '~> 1.0.0'
end
{% endcodeblock %}
编辑完成之后，在测试项目里`pod update`一下，几个子项目都被加进项目工程了，写代码验证无误之后，就可以将这个工程push到远端仓库，并打上新的`tag->1.0.0`。
> 但是如果你的代码版本号没变(podspec里的version自然也没变)就会提示push失败，即使你更改了podspec的其他地方，pod也会认为这两个文件是同一个。 我目前为止找不到trunk的相关update接口，所以只能顺水推舟，更新源代码版本号（如：1.1.1->1.1.2），重新push version tag，然后再执行pod trunk push操作。
