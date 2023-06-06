---
title: AbletonLive10安装使用
date: 2018-06-19 14:23:13
updated: 2018-09-22 21:19:00
comments: true
tags: [智能设备]
categories:
- 解决方案
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## launchPad
[设备官网](https://global.novationmusic.com)

[官方教程资源](https://global.novationmusic.com/launch/launchpad-mini/support-downloads)
[官方工程试听资源支持下载](https://intro.novationmusic.com/join-the-dub-side)

## ableton live 软件
[官网下载](https://www.ableton.com/en/trial/)
[中国社区](http://abletive.com)
[ABLETON LIVE 10.0.1 SUITE 破解版下载 WINDOWS & MAC](https://launchpadxm.com/class/ableton-live-10-0-1-suite-破解版下载.html)
[ABLETON LIVE10.0.1破解机下载](http://p27uwfdhv.bkt.clouddn.com/static/soft/Ableton%2010%20KeyGen%20v1.3.0.zip)


## 下载


## 安装
{% post_path MacOS系统下运行exe小程序 %}
## 灯光

### [第二期：Chain的选择与设置/页数的映射](http://www.bilibili.com/video/av12189950)
1. 音频轨道
拖拽音频文件或视频文件到`Simple`采样轨道中，视频格式会生成`asd`格式的新文件文件，加载到轨道中。

1. MIDI轨道可以为`Lights`
1.1 灯光轨道
1.2 鼓点设置
#### 鼓点音色映射设置，来输出不同的音乐片段
1. 拖动IB控件到MIDI模块

#### 鼓点灯光映射设置
1. 拖进MIDI Effects rack控件
1.1 点击黄色选项：展开Key:vel:chain:Hide页面
1.2 右击蓝色条，选择map selecter
1.3 选中右上角的`MIDI`切换到设置MIDI映射键模式。
1.4 选中第一个表盘，选中LauchPad上的 A—B，来映射页数的设置
1.5 新建八个chain，错位蓝色条，完成页数映射的设置
1.6 逐个选中不同的chain，在每一个chain中嵌套MIDI Effect Rock控件

### [第三期：关于分轨和音频采样](http://www.bilibili.com/video/av12319673)
[reddit.com](https://www.reddit.com/r/SongStems/)
[beatport.com](https://www.beatport.com/stems)
[splice.com](https://splice.com/explore/contests)
选中页数1的chain ，展开drum rack模块，点击launchpad上键盘，会高亮显示映射的鼓点位置，这时将采样的音色拖拽到改鼓点位置，再次点击launchpad键垫就可以播放音色片段。

### [第四期：基础灯光效果制作](http://www.bilibili.com/video/av12431271)
#### Arpeggiator （A效果器）
设置纵横方向的属性变化
效果：垫子灯光会从左到右，从下向上的走马灯式的移动。通过arpeggiator设置移动速度，范围
1. rate速度：1/1一拍移动一下最慢，1/128最快。
2. Gate范围：1—200：依据灯光速率的亮度百分率率来看，1%:亮度不高，200%：可以在一个键上激活两个相邻的灯光
3. style：纵向变化的方向：up/down/upDown/DownUp...
4. Hold激活不用长按，即可厂量
5. repeats：设置走马灯的循环次数

6. A效果器在灯光效果包中，放置的位置不同起到不同效果，例如一个灯光效果在A效果器之后，则会将A效果器的属性应用到后续其他的灯光效果上。

#### Chord （和弦）
1. 拖拽到Key列表中的一个垫子的灯光效果包上。
2. chord提供六个属性shift1，shift2....shift6，来设置和弦灯光错位，融合等效果
第一个旋钮设置1 ：说明灯光向右边移动一个单位。即当点击当前垫子时，右边相邻的垫子的灯光也会一起亮
第二个按钮shift2设置+2：灯光向右联动两个单位，右边相邻的两个垫子的灯光都点亮。
以此类推
一个key上可以添加多个chord：根据偏移量来激活周边的垫子灯光。+4亮起四分之一，+16：亮起半屏，-16：四分之三亮起 -32：整个lPD全亮

#### MIDI Effect Rack
在主MIDI effect Rack中的chain中映射出的页数中，再嵌套子MIDI effect Rack这样每一页都可以设置自己的灯光效果。
设置灯光效果包
1. 选中嵌套的子MIDIeffect rack，展开chain模块 ，右击新建一个chain，即代表着一个灯光效果
2. 选中key，点击lauchpad垫子，在钢琴键位为标红显示，即可定位将要设置灯光效果的键。即：绿色区域定位点击的键垫位置
3. 新建Velocity（力度感应）
4. 新建chord（和弦）
5. 新建Arpeggiator（A效果器）

#### Note Length
延迟灯光时间
Pitch
Random
Scale
#### Velocity：力度感应
1. 拖到刚才的chain上，即在该键上添加力度感应属性设置。
2. 设置灯光颜色：`Out Hi`的表盘参数，参考MIni支持的灯光色值

