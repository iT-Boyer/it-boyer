---
title: 使用gem升级pod方法
date: 2018-10-06 14:50:12
updated: 2018-10-20 10:48:04
comments: true
tags: [shell]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
# 升级cocopods
```shell
sudo gem update --system
gem source -l
pod setup
pod repo update --verbose
sudo gem install cocoapods --pre
sudo gem cleanup
```
gem source
```
$ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
$ gem sources -l
https://gems.ruby-china.com
# 确保只有 gems.ruby-china.com
```
腾讯云：` https://gems.ruby-china.com/`
淘宝：`https://ruby.taobao.org/`
