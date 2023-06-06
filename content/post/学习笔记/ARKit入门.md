+++
title = "元宇宙初探"
description = "iOS端增强现实实现，了解基本的概念和知识领域的价值"
date = 2021-10-20T19:16:00+08:00
publishDate = 2021-10-20T16:58:00+08:00
lastmod = 2022-03-16T08:19:25+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

## I:辨别知识和信息 {#i-辨别知识和信息}

元宇宙现状和技术知识体系 

{{< figure src="/ox-hugo/元世界知识体系.png" >}} 

[主流VR平台和开发引擎简介 - 前沿洞察 - 恒生研究院](https://rdc.hundsun.com/portal/article/574.html) 

未来是XR（VR/MR/XR）的天下，所有AI技术（机器学习）都将成为XR世界的一部分。 

AR是人工智能和人机交互的交叉学科，基础技术包括CV（计算机视觉）、机器学习、多模态融合等，借凌老师去年的一篇文章简单科普AR技术。 

[虚拟现实（VR）和增强现实（AR）背后的核心技术是什么？ - 知乎](https://www.zhihu.com/question/36979454/answer/191543111) 

增强现实（AugmentedReality，简称AR）和虚拟现实（VirtualReality，简称VR）概念的出现已经有了几十年的历史了，然而VR/AR大量出现在科技媒体上吸引各方眼球也就是最近的事情。 

[2021年的元宇宙，1999年的互联网|vr|虚拟世界|ar|马克·扎克伯格_网易订阅](https://www.163.com/dy/article/GMJQ4BNE05118O92.html) 


## ARKit 官方简介 {#arkit-官方简介}

增强现实(AR)描述用户体验，将2D或3D元素从设备的摄像头中添加到实时视图中，从而使这些元素出现在真实世界中。 

ARKit结合了设备运动跟踪，摄像镜头捕捉，先进的场景处理，以及显示方便来简化建立AR体验的任务。 

[ARKit开发文档](https://developer.apple.com/documentation/arkit) 


### 理解AR: Augmented Reality {#理解ar-augmented-reality}

理解AR概念、特性和最佳实践来构建很好的AR体验。 

`ARSession` ：一个共享对象，可以管理增强现实体验所需的设备摄像头和运动处理。 

`ARSessionConfiguration`:只记录设备方向的轨迹的基本配置 

`ARWorldTrackingSessionConfiguration`:一种跟踪设备定位和位置的配置，它可以检测设备摄像头看到的真实表面。 


### AR标准视图具体实例 {#ar标准视图具体实例}

[一个基本的AR体验DEMO](https://developer.apple.com/documentation/arkit/building_a_basic_ar_experience) 

配置一个AR会话并使用SceneKit或SpriteKit来显示AR内容 

1.  [Providing 3D Virtual Content with SceneKit](https://developer.apple.com/documentation/arkit/arscnview/providing_3d_virtual_content_with_scenekit) 
    
    `ARSCNView`:一种显示AR体验的视图，它通过3D SceneKit内容增强了相机视图。
2.  [Providing 2D Virtual Content with SpriteKit](https://developer.apple.com/documentation/arkit/arskview/providing_2d_virtual_content_with_spritekit) 
    
    `ARSKView`:一种显示AR体验的视图，增加了2D SpriteKit内容的摄像头视图。

借助 ARKit 和 Core ML，基于 iOS 11 进行开发。 

iOS 11 为开发者带来了各种可能性。借助 ARKit，开发者可将生动逼真的增强现实带到 app 之中。而有了 Core ML，开发者可利用机器学习来创建各种更智能的 app。 

[进一步了解基于 iOS 的开发](https://developer.apple.com/cn/ios/) 


## A1:激活/追问反思经验 {#a1-激活-追问反思经验}

做个技术开发接触过 Android/iOS、swift/objc/java/asp/web/js开发，要么是前端，后端。 

还有设计类的三剑客flash、photoshops、dreamwear，设计类工具之外，很少再去深入了解其他知识领域的技术实现和开发。 

在十几年的发展虚拟现实和大数据分析越来越流行，现在就这些新领域做些探索。 

对元宇宙初步认识，如果想开发oculus VR 游戏，无线设备依赖android 和双U IDE 开发工具的学习。pc 端游戏平台 steamVR 游戏商放弃了OSX 系统。 

在这种环境下，VR 头显只能尝鲜，玩玩VR 游戏，培养一个爱好，保持身心健康，就是最大的意愿了吧。 


## A2:内化应用知识，谱写愿景 {#a2-内化应用知识-谱写愿景}

肯定的是要学习AR 开发更符合实际，可以做一个课程，学习swift 开发。用swift 做一些力所能及的工作。 

在自我领导和管理做了全面升级之后，是时候把工作重心迁移到编程开发，有目的有计划做一些高效的事情，提高自己效率和学习能力，磨练自制力。 


## 要事第一，制定下一步行动 {#要事第一-制定下一步行动}

阿里资源学习：看书，听讲座，写心得，保持头脑清醒灵活，积累知识，保持输出，培养七习原则，为成为一个高效能人士努力。 

swift 学习计划：演练掌握一项技能的过程，探索新的学习技巧，培养新的开发思维，搭建新语言的知识体系。
