---
title: MPMoviePlayerController遇到的坑
date: 2018-08-31 16:58:22
updated: 2018-08-31 16:58:22
comments: true
tags: []
categories:
- 项目总结
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 问题
1. [MPMoviePlayerController播放过程中自动暂停的问题]
在使用系统播放器MPMoviePlayerController的过程中，会出现播放器自动暂停的情况，有两种解决办法：[参看](https://www.cnblogs.com/elsonpeng/p/5404969.html)
1.1 `在播放开始的时候，设置useApplicationAudioSession ＝ NO;`
1.2 `重启手机，也可以恢复`
2. [why does MPMovieLoadState have state 5?](https://stackoverflow.com/questions/3138660/why-does-mpmovieloadstate-have-state-5)
```
The playState is a bitmask. Any number of bits can be set, such as
MPMovieLoadStatePlaythroughOK | MPMovieLoadStatePlayable
Check for states like this:
MPMovieLoadState state = [playerController loadState];
if( state & MPMovieLoadStatePlaythroughOK ) {
NSLog(@"State is Playthrough OK");
}
```

# 问题2
#### 投影不全屏
现象：投影仪页面未同步现象，全屏查看视频，横屏全屏显示，但投影在大屏的画面没有同步
1. 隔离业务代码，编写demo复现投影问题。[ALMoviePlayerControllerGit库](https://github.com/it-boyer/ALMoviePlayerController/tree/master/ALMoviePlayerControllerDemo)
##### demo问题
解决办法：
注释掉：`- (id)initWithContentURL:(NSURL *)url`方法。demo正常播放
小插曲：遇到`Setting device discovery mode to DiscoveryMode_None`，排查之后不影响播放。故没有继续研究。。。
>教训：必须深入代码联调测试中，本可以通过断点排查，查处url为nil导致demo无法的播放的原因。却长时间纠结在不必要的日志中。

2. 通过在投影仪上联调测试`不影响播放`
```
VAALMoviePlayerController[7438:3348866] [] <<<< AVOutputDeviceDiscoverySession (FigRouteDiscoverer) >>>> -[AVFigRouteDiscovererOutputDeviceDiscoverySessionImpl outputDeviceDiscoverySessionDidChangeDiscoveryMode:]: Setting device discovery mode to DiscoveryMode_None (client: VAALMoviePlayerController)
```
通过百度有两种方式：
1.在viewwillapear中添加`player=nil`
[AVPlayer is not playing in iOS 11](https://stackoverflow.com/questions/47438401/avplayer-is-not-playing-in-ios-11#)
[AVPlayerViewController black screen when swiping on iOS 11](https://stackoverflow.com/questions/46321860/avplayerviewcontroller-black-screen-when-swiping-on-ios-11)

2.将工程设置为`arc=YES`

#### MP4音频不同步
现象：视频播放到待定的时间点，画面卡顿，拖动进度条，声音画面不同步，复现：《视频美国儿童思维导图特训营》第一课：21:00时长复现

