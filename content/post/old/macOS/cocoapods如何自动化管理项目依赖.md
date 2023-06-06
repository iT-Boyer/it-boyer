---
title: cocoapods如何自动化管理项目依赖
date: 2018-10-23 20:29:21
updated: 2018-10-23 20:29:21
comments: true
tags: []
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
通过介绍项目相关的属性配置，来了解cocoapods如何自动化管理项目依赖的。最后会通过自定义ruby脚本来演示。

## cocoapods偶现问题 
`pod install`安装依赖，主要是对`build settings`中的新增依赖配置等，
当cocopad 集成失败时，可以通过以下几步排查 ，也可以尝试清除项目中pod相关的信息，重新`pod install`，了解以下步骤都是很重要的。
1. 添加宏Preprocessor Macros
```
Debug：$(inherited) COCOAPODS=1
Release：$(inherited) COCOAPODS=1
```
2. 设置Other C Flags
```
Debug：$(inherited) -iquote "${PODS_CONFIGURATION_BUILD_DIR}/Small/Small.framework/Headers" -iquote "${PODS_CONFIGURATION_BUILD_DIR}/ZipArchive/ZipArchive.framework/Headers"
Release：$(inherited) -iquote "${PODS_CONFIGURATION_BUILD_DIR}/Small/Small.framework/Headers" -iquote "${PODS_CONFIGURATION_BUILD_DIR}/ZipArchive/ZipArchive.framework/Headers"
```
3. 设置Framework Search Paths
```
Debug：$(inherited) "${PODS_CONFIGURATION_BUILD_DIR}/Small" "${PODS_CONFIGURATION_BUILD_DIR}/ZipArchive"
Release：$(inherited) "${PODS_CONFIGURATION_BUILD_DIR}/Small" "${PODS_CONFIGURATION_BUILD_DIR}/ZipArchive"
```
4. Add User-Defined setting,新加三个参数
    1. PODS_CONFIGURATION_BUILD_DIR
    Debug:   ${PODS_BUILD_DIR}/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)
    Release: ${PODS_BUILD_DIR}/$(CONFIGURATION)$(EFFECTIVE_PLATFORM_NAME)
    PODS_PODFILE_DIR_PATH: ${SRCROOT}/../..
    2. PODS_ROOT : ${SRCROOT}/../../Pods

### 使用脚本自动设置 
`Small-subprojects.rb`编辑项目配置文件，动态设置插件库的`Framework search path`.
在项目中**run shellScript**添加`ruby Small-subprojects.rb`命令:
1. 当old_fsp为空时，会执行失败。
2. 使用`./Small-subprojects.rb`，有时出现权限问题，替换为`ruby Small-subprojects.rb`。
3. 编码问题：在头部添加`# encoding: utf-8`：如`invalid byte sequence in US-ASCII (ArgumentError)`
```
#!/usr/local/bin ruby
# encoding: utf-8
require 'xcodeproj'
require 'xcodeproj/project/object/target_dependency'

project_path = "#{Dir.pwd}/#{Dir['*.xcodeproj'][0]}"
puts "项目位置：#{project_path}"
project = Xcodeproj::Project.open(project_path)
project.native_targets.each do |target|
    puts "项目target：#{target}"
    target.dependencies.each do |dep|
        puts "项目dep：#{dep}"
        if (dep.name != nil)
            changed = false
            sub_project = dep.target_proxy.proxied_object.project
            puts "sub_project：#{sub_project}"
            sub_project.native_targets.each do |sub_target|
                sub_target.build_configurations.each do |config|
                    old_fsp = "ddd"#config.build_settings['FRAMEWORK_SEARCH_PATHS']
                    puts "旧old_fsp：#{config.build_settings}"
                    if (!(old_fsp.include? "$(CONFIGURATION_BUILD_DIR)/**"))
                        changed = true
                        puts "更新-----"
                        #config.build_settings['FRAMEWORK_SEARCH_PATHS'] << "$(CONFIGURATION_BUILD_DIR)/**"
                        # puts "Small: Add framework search paths for '#{dep.name}'"
                    end
                end
            end

            if (changed)
                sub_project.save
            end
        end
    end
end
```
打印信息如下：
```
Showing All Messages
:-1: 项目位置：/Users/admin/hsg/hexo/XcodeTool/iSmallApp/iSmallApp.xcodeproj

:-1: 项目target：iSmallApp

:-1: 项目dep：IntelDecision

:-1: sub_project：#<Xcodeproj::Project> path:`/Users/admin/hsg/hexo/XcodeTool/iSmallApp/bundles/IntelDecision/IntelDecision.xcodeproj` UUID:`84C839BA217F28A500541D4D`

:-1: 旧old_fsp：{"CODE_SIGN_IDENTITY"=>"", "CODE_SIGN_STYLE"=>"Automatic", "DEFINES_MODULE"=>"YES", "DEVELOPMENT_TEAM"=>"9CA5KUE8T7", "DYLIB_COMPATIBILITY_VERSION"=>"1", "DYLIB_CURRENT_VERSION"=>"1", "DYLIB_INSTALL_NAME_BASE"=>"@rpath", "INFOPLIST_FILE"=>"IntelDecision/Info.plist", "INSTALL_PATH"=>"$(LOCAL_LIBRARY_DIR)/Frameworks", "LD_RUNPATH_SEARCH_PATHS"=>["$(inherited)", "@executable_path/Frameworks", "@loader_path/Frameworks"], "PRODUCT_BUNDLE_IDENTIFIER"=>"com.clcw.IntelDecision", "PRODUCT_NAME"=>"$(TARGET_NAME:c99extidentifier)", "SKIP_INSTALL"=>"YES", "SWIFT_VERSION"=>"4.2", "TARGETED_DEVICE_FAMILY"=>"1,2"}

:-1: 更新-----

:-1: 旧old_fsp：{"CODE_SIGN_IDENTITY"=>"", "CODE_SIGN_STYLE"=>"Automatic", "DEFINES_MODULE"=>"YES", "DEVELOPMENT_TEAM"=>"9CA5KUE8T7", "DYLIB_COMPATIBILITY_VERSION"=>"1", "DYLIB_CURRENT_VERSION"=>"1", "DYLIB_INSTALL_NAME_BASE"=>"@rpath", "INFOPLIST_FILE"=>"IntelDecision/Info.plist", "INSTALL_PATH"=>"$(LOCAL_LIBRARY_DIR)/Frameworks", "LD_RUNPATH_SEARCH_PATHS"=>["$(inherited)", "@executable_path/Frameworks", "@loader_path/Frameworks"], "PRODUCT_BUNDLE_IDENTIFIER"=>"com.clcw.IntelDecision", "PRODUCT_NAME"=>"$(TARGET_NAME:c99extidentifier)", "SKIP_INSTALL"=>"YES", "SWIFT_VERSION"=>"4.2", "TARGETED_DEVICE_FAMILY"=>"1,2"}

:-1: 更新-----

:-1: 项目target：iSmallAppTests

:-1: 项目dep：TargetDependency

:-1: 项目target：iSmallAppUITests

:-1: 项目dep：TargetDependency
```


