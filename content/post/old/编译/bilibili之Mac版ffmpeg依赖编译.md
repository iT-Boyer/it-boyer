---
title: bilibiliForMac版之mpv依赖编译
date: 2017-02-10 10:45:03
updated: 2017-05-26 18:13:12
comments: true
tags: [ffmpeg,直播,macos]
categories: 
- 解决方案
keywords: 编译,ffmpeg,mpv,macOS,视频
permalink: 
---

## [Bilibili Mac Client](https://github.com/typcn/bilibili-mac-client)
一款基于mpv和ffmpeg底层库实现的播放器.

## 项目依赖库 
编译在项目中所需的相关依赖库
### 下载资源
1. 下载mpv-build
{% codeblock  lang:git %}
git clone https://github.com/mpv-player/mpv-build.git
{% endcodeblock %}

2. 执行下载`ffmpeg`, `libass` 和`mpv资源`，同时完成编译的命令：
{% codeblock  lang:bash %}
cd mpv-build/ 
./rebuild -j4  
{% endcodeblock %}

3. 使用参数 “--enable-shared ” 可以开启 ffmpeg 的动态版本:
{% codeblock lang:bash  %}
cd ffmpeg/
./configure --enable-shared    
{% endcodeblock %}

4. 安装ffmpeg
{% codeblock  lang:bash %}
在ffmpeg目录下执行：
make 
make install
{% endcodeblock %}

### 开始编译mpv动态依赖库：
1. 开启libmpv动态库的支持：
{% codeblock lang:bash %}
cd ../mpv/
./waf configure --enable-libmpv-shared  --disable-libass
./waf build
{% endcodeblock %}
> 变更去除static参数：./waf configure --enable-static-build --enable-libmpv-shared  --disable-libass

## 集成到项目中
### 指定libmpv.dylib相对路径
直接编译出来的库会是绝对路径，需要先通过install_name_tool 修改 相对路径：
{% codeblock  lang:shell %}
cd build/
install_name_tool -id "@executable_path/lib/libmpv.dylib" libmpv.dylib
{% endcodeblock %}


### 聚合ffmpeg相关依赖包
执行 mpv`tools/dylib-unhell` ，目标是 `libmpv.dylib`
{% codeblock lang:bash %}
TOOLS/dylib-unhell.py libmpv.dylib
{% endcodeblock %}
这样会多出一个 `lib文件夹`，里面会出现变为相对路径的文件，复制导入到项目即可。

## 相对路径脚本学习
使用otool -L 和install_name_tool完成了一系列操作：
{% codeblock lang:bash %}
install_name_tool -change
install_name_tool -id 
{% endcodeblock %}
![编译后的资源目录位置](https://cloud.githubusercontent.com/assets/4022953/16513398/fba07b2a-3f96-11e6-8358-b93275ed0a09.png)
### 扩展一：
{% codeblock  lang:shell %}
#!bin/sh
mkdir "$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/dylib"
cp -f /your/path/to/xcode_project_name/dylib/*.dylib "$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/dylib/"

echo "--------$(pwd)----------------"
cur_dir="$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/MacOS/lib"
cd ${cur_dir}
echo "--进入$(pwd)--"

for dirlist in $(ls ${cur_dir})
    #查看它们的 rpath
    otool -L ${dirlist}
    #制作相对路径
    #方法一
    install_name_tool -change /usr/local/lib/${dirlist} @executable_path/lib/${dirlist} "$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/MacOS/$PRODUCT_NAME"
    #方法二
    install_name_tool -id "@executable_path/lib/${dirlist}" ${dirlist}
do

done

{% endcodeblock %}
### 扩展二
{% codeblock  lang:shell %}
echo "--------$(pwd)----------------"
cur_dir="$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/MacOS/lib"
cd ${cur_dir}
echo "--进入$(pwd)--"
lib="libmediainfo.0.dylib"
#-f 参数判断 $file 是否存在
if [ -f "$lib" ]; then
    otool -L ${lib}
    install_name_tool -id "@executable_path/lib/${lib}" ${lib}
    otool -L ${lib}
fi
{% endcodeblock %}



