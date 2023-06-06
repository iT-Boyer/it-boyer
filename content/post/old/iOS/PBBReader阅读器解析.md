---
title: PBBReader阅读器解析
date: 2017-06-26 17:02:18
updated: 2017-06-26 17:12:47
comments: true
tags: [iOS]
categories:
- 项目总结
keywords:
permalink:
---
 
## 支持OS X浏览PDF简单视图
运行scheme： `PDFReaderForOSX` 即可


## 支持iOS

pageViewController: pdf翻页效果视图控制器
startingViewController:DataViewController,翻页视图控制器的视图源
modelController:ModelController:NSObject,数据视图数据源的model模型。
```puml

title 阅读器的内部结构图
center header
解析PDF目录，显示PDF页面
endheader

class FileViewController<UIViewController>{
-- 属性组 --
+IBOutlet UIView *pdfView;
+UIPageViewController *pageViewController;
-startingViewController;
__ 函数组 __
- (IBAction)ibaBookmarks:(UIButton *)sender;
- (IBAction)ibaOutline:(UIButton *)sender;
- (IBAction)ibSearchBtn:(id)sender;
}

class UIPageViewController<pageView>{
-- 属性组 --
+delegate;
+dataSource;
+view.frame;
+gestureRecognizers;
__ 函数组 __
+init()
}

class startingViewController<dataSource>{
__ 函数组 __
-(init)viewControllerAtIndex:storyboard:
-- 搜索模块 --
-(void)restSearchResultColor:(NSString *)searchStr;
}

'关系层'
startingViewController .left.> UIPageViewController:model聚合数据源
UIPageViewController ..>FileViewController:手势传递
note on link #red: 把翻页控制器的手势事件\n传递给阅读器的PdfView中

FileViewController "1" *-[#blue]- "1"UIPageViewController
note on link #blue: 构造实例为阅读器添加翻页功能

startingViewController .left[#gray].> "1"FileViewController:pageViewController的数据源
note on link #gray: 仅用于初始化第一次显示的PDF阅读器界面内容

FileViewController --> startingViewController: 搜索bar传递字符串 >



note right of startingViewController #white: 1. 为翻页提供数据源\n 2. 更新搜索字段颜色
note left of FileViewController #red : PDF阅读器主页面
note left of UIPageViewController : 实现翻页效果的控制器

class PDFParser<工具类>{
__ 函数组 __
-(id)initWithFileName:(NSString *)filename;
-(id)initWithPDFDoc:(CGPDFDocumentRef)doc;
-(NSArray *)getPDFContents;
}

class OutLine<UIViewController>{
__ 属性组 __
NSMutableArray *outlineEntries;
NSMutableArray *openOutlineEntries;
IBOutlet UITableView *outlineTableView;
NSObject<MyOutlineViewControllerDelegate> *delegate;
}
'关系图'
PDFParser --> OutLine:解析PDF文档获取目录结构
OutLine -.> FileViewController: 提供目录界面

note left of PDFParser #green
解析pdf目录结构的核心工具包
end note
note right of OutLine #white : 创建目录界面\n显示解析PDF目录工具中得到的数据

center footer
了解具体的项目结构
endfooter
```


