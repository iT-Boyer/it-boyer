---
title: PBBReader项目重构模型
date: 2017-07-25 17:06:47
updated: 2018-09-22 21:18:51
comments: true
tags: []
categories:
- 项目总结
keywords: 
permalink:
---

![](http://www.plantuml.com/plantuml/png/ZLPTJnj757tVNp5ofQpKKdYib0Sa24AXAOq5g9Ng-c5j3_OgQw_h7VgX20a41lDXC8428ON458gRWasB-C0E-C_iZ7UV-XTwlRFsZZ6ccGVmd7lcp9cPEtUMg4J3oD_VFuAL24xP-UlQcw8zdsLxiwoGftjBKXWd24wXc1EN7cBYHCHbIVAdl27w-odRNgbLPitAHPsskuN2UZof9q2r9y1ncii6aNbrmfQEwUebsoYnvUdl4ooxR-MNaIiaW-Rdq5n1UZ3FpWgF-VXQkShxTBLaLWxjgHshEc-ztW7SrztjiwfxREMGPavf-iAiRDBIjVLt7gH04aG_gJU4k83hqBp9rcwA_txOqV0uwKcfqHZf8Ngo9wGJeiSHSiOumoNvaTCGTm9_QIxZgl25y0uzNwQ7M-WHRx8aq_H9xIZhyjzTqW-hZx_M8xXh18U9aeXgk6jGrs0hkmPJIXntFT7qYKZNW4AKa69WMN9GvAEv4bqizFdzlfSIWXQ0dmDOmr65u21I3FILCf48nm2L9YKkY7prZPfRvhuQuzZnlbLTiVFdGGHx7A2bkTgxQRPmIz_irS7xITLjgtZAgHv97EfHYyiia-CJUrHHe3DrihdOnRVMp0tadMkwiHwQkJ1lVcjP3vS_sA7lNvatw_RK0blwipc3LRobnHssEsE_pd4plBL7kA63JRgwHZDpPlalaTuUuVJBVm2qfwPfPilA5veZ9biyszjfgrHfzmoZ8LVPneMdTrsbQulYn0B6AUpL2TkujOxcxFsrZo3QRy_-btY-QXrb_0E37Up32cihfsDAIFJEGSzuUSjUBrkbFzXCkeECUVNUB9VXvjFgp1qnCSJrE09715Up8JOfDQu4yXa_Q2h10_q-z59omdBAAKRirrEMBTBokWDw_6ObWm5EI7J6nOG8TnLHzE0T97G7YEiHh7dOfB1iMKzeQW8_OEYvGn2d3T3RDEIajs39AGN85oCa-PcXXrzWukwR6n0nBb6NvwXmuc7mANHr4iCfeP-S26bgM5D3uhc9qpmT6_Et6EqR7NeAdWIdzOA6VBNZO_DgWMrUZmuyQvQRlMcQNc0h1RWdGGwDnfGK1ehRZKya8-D1JuMcZsiR1NfvIDEN3PKd4mQKuw7der_qQJX5lb7nJ-veeiPnod0xVP4V8QeQQY8g2FBtoxpTjSutNK4vCFhTi1vIDSS799rNFhaIdD2myWAdxed9BS7U3bKcCFJbis4SKHM_flpIGQUTqjj-nRrrMEUph9npLp3kfkzGV-jTggX-9IAShXzsHqyecW2DuAXga9OSC99A60lzRx46RpVc-oncXJXpN8bYe_ldi68Oc1ZtsE2CUyouWNPBNa0qvg7jzeGOJqMvjzaGekcpsfbRV17o8hBKF0lOTV6zazgU9I2qlp0FCCMwBZMJ7OBrJmmuuVPdHinRu3bV8K0K7uIX_WQExkoxnDEYATzyEzs0foT-3JI-zSPr7Vv98HqIaB-kzMyxZlu7)

```puml
title PBBReader项目重构模型
center header
PBBReader项目重构模型
endheader

'******* 声明组件模块 component/[组件名] 中括号支持\n换行 *******'

'---- 声明备注:组件线备注可以通过虚线".."连接到其他对象---'



'#####  备注模块 位置：left/right/top/bottom  #####'




'&&&&&& 组件组合模块 支持模块嵌套 &&&&&&&'
'六种组合样式:Node,Rectangle,Folder,Frame,Cloud,Database'
Frame "启动APP"{
    [APPDelegate] as LaunchAPP
}
Folder "注册模块"{
    [欢迎页] -- [密码找回页]
    [欢迎页] -- [忘记密码页]
    [密码找回页] -- [完成注册页]
    [忘记密码页] -- [完成注册页]
}
Frame "功能项"{
    Folder "制作模块"{
        [多媒体选择页] -- [设定权限页]
        [设定权限页] -- [外发分享页]
        [外发分享页] -- [已发送列表页]
    }
    Folder "阅读模块"{
        [已接收页] -- [广告页]
        [广告页] -- [播放视频页]
        [广告页] -- [播放音频页]
        [广告页] -- [浏览PDF页]
    }
    Folder "发现Tab"{
        [发现页] -- [店铺详情页]
    }
    Folder "个人中心Tab"{
        [个人中心] -- [个人设置]
    }
}
Database "sqliteDB" {

    'reader数据库
    Database "PBBReader" {
        Folder "sqlite" {
            [dao]
            [db]
            [model]
        }
    }
    'online数据库
    Database "PBBOnline" {
        [待定]
    }
}

Cloud "http+socket"{
    Cloud "socket"{
        [basesocket]
        [code]
        [other]
        [publiclib]
    }
    
    Cloud "HTTP" {
        [ASIHttp]
    }
}

Node "第三方SDK"{
    [极光推送]
    [ShareSDK]
    [mupdf]
}

Node "公用工具"{
    [CustomIOS7AlertView]
    [timers]
    [Advertising]
}

Node "项目依赖"{
    '[SZMobileSDK] -- LaunchAPP
    [PBBMaker] -- LaunchAPP
    '[PBBMaker] --> [设定权限页]
    [IJKMediaPlayer] -- LaunchAPP
    '[IJKMediaPlayer] --> [播放视频页]
}

Node "资源"{
    folder "IB"{
        [主iPad]
        [Personal]
        [Register]
        [space]
        [WelcomeView]
    }
    
    folder "Images.xcassets"{
        [主iPadImages]
        [PersonalImages]
        [RegisterImages]
        [spaceImages]
        [WelcomeViewImages]
    }
}

'>>>>>>>>>>  关系模块  >>>>>>>>>>'
LaunchAPP -- [欢迎页]
[完成注册页] -- [多媒体选择页]
[完成注册页] -- [已接收页]
[ShareSDK] -left-> [外发分享页]
[Advertising] -right-> [广告页]
IB -right- Images.xcassets
资源 -left-> 功能项
资源 -right-> 注册模块
center footer
boyer制作
endfooter
```



