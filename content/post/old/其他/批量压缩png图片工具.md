---
title: 批量压缩png图片工具
date: 2018-10-15 15:21:53
updated: 2018-10-15 15:21:53
comments: true
tags: [工具]
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
{% github it-boyer  width = 30% %}

## pngquant
使用pngquant批量压缩png
1. 编写批量处理脚本
`vi compresspng.py`
```py

import os
import sys
## 参考https://www.jianshu.com/p/bfa29141437e
# 执行文件路径:
    # os.path.realpath(__file__)
    # sys.argv[0]
# 当前图片目录:os.getcwd()
# 文件目录:sys.path[0]

# 获取终端参数
#   sys.argv[1]
# 用法
# pngTo imgDir 默认目录路径

def GetFileFromThisRootDir(dir, ext = None):
    allfiles = []
    needExtFilter = (ext != None)
    for root,dirs,files in os.walk(dir):
        for filespath in files:
            filepath = os.path.join(root, filespath)
            extension = os.path.splitext(filepath)[1][1:]
            if needExtFilter and extension == ext in ext:
                allfiles.append(filepath)
    return allfiles

if __name__ == '__main__':
    rootDir=sys.path[0]
    PngquantExe = rootDir + "/pngquant"
    print(len(sys.argv))
    if len(sys.argv) == 1:  #当没有传目录参数时,默认获取当前目录
        srcDir = os.getcwd()
    else:
        srcDir=sys.argv[1]  #获取用户指定的目录路径
    print(srcDir)
    imgFiles=GetFileFromThisRootDir(srcDir, 'png')
    suffix="_temp.png"
    for f in imgFiles:
        cmd = "\"" + PngquantExe + "\"" + " --ext " + suffix + " --force --speed=3 "+ f
        os.system(cmd)
        os.remove(f)
        newfile=f.replace(".png", suffix)
        os.rename(newfile, f)
print("压缩完成")
```
用法
 1. `pngTo`默认压缩当前目录的图片
 2. `pngTo imgDir` imgDir用户指定的目录名

参考[使用pngquant批量压缩png](https://www.jianshu.com/p/260ed2ab5f57)

## tinypng
https://tinypng.com/developers
[python 脚本批量处理](https://www.jianshu.com/p/b15ebd623650)
