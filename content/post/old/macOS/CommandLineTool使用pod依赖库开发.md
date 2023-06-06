---
title: CommandLineTool使用pod依赖库开发
date: 2018-10-16 17:08:15
updated: 2018-10-16 17:08:15
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
## 问题
[#3707](ttps://github.com/CocoaPods/CocoaPods/issues/3707)
```
dyld: Library not loaded: @rpath/FilesProvider.framework/Versions/A/FilesProvider
```
结论：cocoa pods不支持`Command Line Tool`项目

[Cocoapods + Command Line Tool - dyld: Library not loaded: @rpath/Realm.framework/Versions/A/Realm](https://stackoverflow.com/questions/41258187/cocoapods-command-line-tool-dyld-library-not-loaded-rpath-realm-framework)
cocoa pods不支持`Command Line Tool`项目
[Command Line Tool + CocoaPods frameworks breaks](https://github.com/CocoaPods/CocoaPods/issues/3707)

## 采用SPM来管理依赖库
1. 使用SPM创建可执行模版
```
$ mkdir SPMCmdLineTool
$ swift package init --type=executable
```
2. 配置`Package.swift`
```swift
import PackageDescription
let package = Package(
    name: "SPMCmdLineTool",
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
        .package(path: "/Users/admin/hsg/FileProvider")
    ],
    .target(
        name: "SPMCmdLineTool",
        dependencies: ["FilesProvider"]),
        
    .testTarget(
        name: "SPMCmdLineToolTests",
        dependencies: ["SPMCmdLineTool"]),
    ]
)
```
3. 使用workspace管理
先生成一个Xcode工程文件：`SPMCmdLineTool.xcodeproj`
```sh
$ swift package generate-xcodeproj
```
再把`SPMCmdLineTool.xcodeproj`拖入现有的workspace中来管理。
> 每次执行swift build ，须重新生成xcodeproj工程文件，再重新打开workspace即可。

## 命令行的单元测试赏析
```swift
final class SPMCmdLineToolTests: XCTestCase {
    func testExample() throws {
        // This is an example of a functional test case.
        // Use XCTAssert and related functions to verify your tests produce the correct
        // results.

        // Some of the APIs that we use below are available in macOS 10.13 and above.
        guard #available(macOS 10.13, *) else {
            return
        }
        // 运行可执行文件
        let fooBinary = productsDirectory.appendingPathComponent("SPMCmdLineTool")

        let process = Process()
        // 指定可执行文件
        process.executableURL = fooBinary

        let pipe = Pipe()
        process.standardOutput = pipe
        //开始运行
        try process.run()
        process.waitUntilExit()

        let data = pipe.fileHandleForReading.readDataToEndOfFile()
        let output = String(data: data, encoding: .utf8)

        XCTAssertEqual(output, "Hello, world!\n")
    }

    /// Returns path to the built products directory.
    var productsDirectory: URL {
        #if os(macOS)
            for bundle in Bundle.allBundles where bundle.bundlePath.hasSuffix(".xctest") {
                return bundle.bundleURL.deletingLastPathComponent()
            }
            fatalError("couldn't find the products directory")
        #else
            return Bundle.main.bundleURL
        #endif
    }

    static var allTests = [
        ("testExample", testExample),
    ]
}
```




