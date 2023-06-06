---
title: swift调用pngquant批量压缩图片
date: 2018-10-15 20:51:45
updated: 2018-10-15 20:51:45
comments: true
tags: [macOS,工具]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
abstract: 欢迎光顾，输入码阅读
message: 欢迎光顾，输入码阅读
top:   
---
<!--github库卡片-->
{% github amosavian FileProvider b597244 width = 30% %}
## 安装FilesProvider
使用`FilesProvider`来做文件处理，通过SPM管理库依赖
1. 配置`Package.swift`
```json
dependencies: [
    // Dependencies declare other packages that this package depends on.
    // .package(url: /* package url */, from: "1.0.0"), 指定版本。giturl
    .package(path: "/Users/admin/hsg/FileProvider")   //源码clone本地，指定路径
],
    .target(
        name: "SPMCmdLineTool",
        dependencies: ["FilesProvider"]),
]
```
2. `swift build`
## 单元测试
1. `FilesProvider`异步处理：需要`XCTestExpectation`辅助测试
2. `FilesProvider`在处理文件（拷贝/重命名/删除）时，不能使用绝对路径，应采用文件相对于`documentsProvider.baseURL`的相对路径。否则，在当给定的路径包含root目录时，例如`/Users/nam/file.png`会提示失败。
3. 在swift调用shell时，目前只能使用`Process.launchedProcess`类方法执行shell脚本命令。
    有可能在单元测试环境，导致其他两种调用shell的失败。

## 测通可运行的代码片段
```swift
import FilesProvider
var documentsProvider:LocalFileProvider!
var pngexpectation: XCTestExpectation! = nil
override func setUp() {
    // Put setup code here. This method is called before the invocation of each test method in the class.
    documentsProvider = LocalFileProvider(for: .userDirectory, in: .allDomainsMask)
    pngexpectation = self.expectation(description: "BLDownloadImageNotification")
}

func testExample() {
    // This is an example of a functional test case.
    // Use XCTAssert and related functions to verify your tests produce the correct results.
    //创建文件夹
    documentsProvider.create(folder: "testPng", at: "admin/hsg/") { (err) in
        print("新建目录成功")
        //拷贝图片到目录
        self.documentsProvider.contentsOfDirectory(path: "admin/Desktop",           
            completionHandler: { (contents, error) in
            for file in contents
            {
                let png = file.path.hasSuffix("png")
                if png{
                    print("文件路径:\(file.path)")
                    let thePath:NSString = file.path as NSString
                    let toFile = "admin/hsg/testPng/\(thePath.lastPathComponent)"
                    self.documentsProvider.copyItem(path: file.path, to: toFile, overwrite: true, completionHandler: { (err) in
                        //调用shell工具压缩图片
                        self.testProcessRunShellScript(filePath: toFile)
                        print("Name: \(file.name)")
                        print("Size: \(file.size)")
                        print("路径: \(file.path)")
                    })
                }
            }
        })
    }
    waitForExpectations(timeout: 40, handler: nil)
}
//可用
func testProcessRunShellScript(filePath:String) {
    let sub = "_temp.png"
    let pngquantFile = "/Users/"+filePath
    let tmpFilePath = filePath.replacingOccurrences(of: ".png", with: sub)
    let exePath = "/Users/admin/hsg/hexo/GitSubmodules/hsgTool/pngquant/pngquant"
    Process.launchedProcess(launchPath: exePath, arguments: ["--ext",sub,"--speed=3",pngquantFile])
    // 删除旧文件
//        documentsProvider.removeItem(path: filePath) { (error) in
//            //重命名压缩过的tmp文件
//            print("ddddddderr:\(error?.localizedDescription)")
//            self.documentsProvider.moveItem(path: tmpFilePath, to: filePath, overwrite: true,  completionHandler: { (error) in
//                self.pngexpectation.fulfill()
//            })
//        }
}
```

