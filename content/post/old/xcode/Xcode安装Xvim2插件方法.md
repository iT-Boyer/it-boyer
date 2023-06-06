+++
title = "XVim 插件"
lastmod = 2020-07-19T21:40:00+08:00
draft = false
author = "iTBoyer"
#password = 1234
+++

-   Xcode 重签名证书：钥匙串访问—证书助理：创建证书 — 名称：XcodeResign  类型：代码签名签名：sudo codesign -f -s XcodeResign（证书名） /Applications/Xcode.app

签名问题  
/Applications/Xcode.app: replacing existing signature  
/Applications/Xcode.app: resource fork, Finder information, or similar detritus not allowed  
解决命令：  
sudo xattr -cr /Applications/Xcode.app  

查看 Xcode 签名 UUID：defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID  
替换插件的 UUID：  
find ~/Library/Application\\ Support/Developer/Shared/Xcode/Plug-ins -name Info.plist -maxdepth 3 | xargs -I{} defaults write {} DVTPlugInCompatibilityUUIDs -array-add `defaults read /Applications/Xcode.app/Contents/Info DVTPlugInCompatibilityUUID`  

安装 Xvim2  
git clone <https://github.com/XVimProject/XVim2.git>  
cd XVim2  
git checkout xcode10.1  
make  
defaults delete  com.apple.dt.Xcode DVTPlugInManagerNonApplePlugIns-Xcode-x-x  
完成后，重启 Xcode
