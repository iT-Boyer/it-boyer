---
title: 为Pod的库创建演示文件SwiftPlayground
date: 2018-10-12 19:56:59
updated: 2018-10-12 19:56:59
comments: true
tags: [swift,pod]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---

<!--github库卡片-->
{% github asmallteapot cocoapods-playgrounds c54b492 width = 30% %}
[issues 62](https://github.com/asmallteapot/cocoapods-playgrounds/issues/62#issuecomment-427543129)

[在playground中优雅的使用Pod](https://www.jianshu.com/p/8e7599c27542)
# This Could Be Us But You Playing

[![Build Status](https://img.shields.io/travis/segiddins/ThisCouldBeUsButYouPlaying/master.svg?style=flat)](https://travis-ci.org/segiddins/ThisCouldBeUsButYouPlaying)

![](README_images/alamofire.png)

Generates a Swift Playground for any Pod.

## Installation

$ gem install cocoapods-playgrounds

## Usage

### CocoaPods

To generate a Playground for a specific Pod:
```
$ pod playgrounds Alamofire
```
To generate a Playground for a local development Pod:
```
$ pod playgrounds ../../../Sources/Alamofire/Alamofire.podspec
```
To generate a Playground with multiple Pods:
```
$ pod playgrounds RxSwift,RxCocoa
```
### Carthage

To generate a Playground for a Carthage-enabled library:
```
$ carthage-play Alamofire/Alamofire
```
Note: This currently assumes that libraries are hosted on GitHub.

### CLI

To generate an empty Playground from the commandline:
```
$ playground --platform=ios YOLO
$ open YOLO.playground
```
