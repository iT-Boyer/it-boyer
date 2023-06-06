---
layout: post
title: TextKit之便笺实战
date: 2014-07-03 17:29:00 +0800
comments: true
tags: [API]
categories:
- 项目总结
---


## 便笺练习功能点:
通过实现以下特效，练习并掌握布局管理器（layout manger），文本容器（text containers）和文本存储器（text storage）等用法。
  * 动态样式（Dynamic type）  
  * 凸版印刷效果（Letterpress effects）  
  * 环绕路径（Exclusion paths）  
  * 动态文本格式及存储（Dynamic text formatting and storage）  
  
这个应用中我们将实现回流文本，字体大小的动态变换，以及闪回文本等效果。
效果图:  
![image](/images/bianqian.png)  
App开始运行后自动生成一组便笺实例并利用`tableViewController`显示出来。`Storyboards`和`segues`会将被选中的单元格所对应的便笺内容显示出来以供用户编辑。
项目开发包：[Notepad.zip](http://cdn4.raywenderlich.com/wp-content/uploads/2013/09/TextKitNotepad-starter.zip)

## 动态样式
`动态样式（Dynamic type）`是iOS 7里面变化最大的特性之一; 它使得app可以遵从用户选择的字体大小和粗细。
选择 **通用->文字大小** 或 **通用->辅助功能** 来查看app中的字体设置。

![image](/images/UserTextPreferences.png)  
iOS 7 支持通过`粗体`、`设置字体大小`等方式提高支持动态文本的应用的易读性。
例如**`UIFont`**新增的一个方法： **`preferredFontForTextStyle`** 用来根据用户对字体大小的设置来自动制定字体样式。  
下面表格中是六种可用字体样式的示例：  
![image](/images/TextStyles.png)  
最左边一列是最小字体；中间一列是最大字体；最右边一列是粗体效果。  

### 使用系统动态字体样式
使用动态文本，是通过给`文本字体`设置字体样式**style**而不是指定具体的`字体名称`和`大小`。这样，系统会在运行时自动根据这一样式以及用户的字体大小设置来选择使用合适的字体。

#### `preferredFontForTextStyle:`方法设置字体样式

1. 打开 `NoteEditorViewController.m/swift` 在`viewDidLoad：`方法实现的最后面加入以下代码：
{%codeblock lang:objc%}
self.textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
{%endcodeblock%}
{% codeblock  lang:swift %}
self.textView.font = UIFont.preferredFont(forTextStyle: .body)
{% endcodeblock %}
2. 打开 `NotesListViewController.m/swift` 在 `tableView:cellForRowAtIndexPath:` 方法中增加如下代码:
{%codeblock lang:objc%}
cell.textLabel.font = [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline];
{%endcodeblock%}
{% codeblock  lang:swift %}
cell.textLabel?.font = UIFont.preferredFont(forTextStyle: .headline)
{% endcodeblock %}
上面两行代码都用到了新版iOS的字体样式.   
> 字体样式：通过语义法命名字体，例如 `UIFontTextStyleSubHeadline`, 可以避免在代码里每一处都指定具体的字体名称和样式， 而且确保app能对用户的字体大小设置做出恰当的回应。

#### APP响应用户字体设置
1. 设置系统字体
返回到**通用->文字大小**重新修改字体设置. 
再运行App, **Note**页面的文字大小是当前设定的字体大小；前后截屏对比,分辨率小了一半。  
![image](/images/NotepadWithDynamicType.png) 
2. 设置系统字体生效
当我们返回到**通用->文字大小**重新修改字体设置. 再打开**Note**页面, 会发现app并没有**立即**对字体设置的变化做出相应反应。

##### 监听系统通知：实现APP响应用户字体设置
当用户修改了他们的字体大小设置之后，这一样式对应的字体并不会自动更新，必须重新请求才能获取新的值。用户设置变化后，`preferredFontForTextStyle:`方法返回的字体也会变化。
1. 添加监听系统通知`UIContentSizeCategoryDidChangeNotification`通知APP响应用户字体设置的变化
打开 `NoteEditorViewController.m` 并在 `viewDidLoad` 方法的实现的最后加入以下代码：
{%codeblock lang:objc%}
[[NSNotificationCenter defaultCenter]
                              addObserver:self
                                 selector:@selector(preferredContentSizeChanged:)
                                     name:UIContentSizeCategoryDidChangeNotification
                                   object:nil];
{%endcodeblock%}
{%codeblock lang:swift%}
//字体变化通知:调用preferredContentSizeChanged:方法
NotificationCenter.default.addObserver(self, selector: #selector(NoteEditorViewController.preferredContentSizeChanged(_:)), name: NSNotification.Name.UIContentSizeCategoryDidChange, object: nil)
{%endcodeblock%}
2. 添加系统通知响应事件
收到用于指定本类接收字体设定变化的通知后，调用`preferredContentSizeChanged:`方法
在`NoteEditorViewController.m`中`viewDidLoad`方法之后紧接着添加以下方法：
{%codeblock lang:objc%}
- (void)preferredContentSizeChanged:(NSNotification *)notification
{
    self.textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
}
{%endcodeblock%}
{%codeblock lang:swift %}
//字体变化通知时调用
func preferredContentSizeChanged(_ notification:NSNotification)
{
    self.textView.font = UIFont.preferredFont(forTextStyle: .body)
}
{%endcodeblock%}
这一方法作用是根据新的字体设置来设定`textView`中的字体。    
Build并运行app，修改字体大小设置，Note页面就可以即时更新字体大小了。
<!--more-->

### 更新布局
现在，如果你把字体设置到很小，那每个单元格的空白区域是不是太多了，看上去文字比较稀疏，如下面所示：  
  ![image](/images/ChangingLayout.png)  
      
这是**动态样式**有点小复杂的部分：要保证App在字体大小变化后，同时也修改文字表格的行高。  
在`NotesListViewController.m`中实现`tableView:heightForRowAtIndexPath:` 代理方法:
{%codeblock lang:objc%}
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    static UILabel* label;
    if (!label) {
        label = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, FLT_MAX, FLT_MAX)];
        label.text = @"test";
    }
    label.font = [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline];
    [label sizeToFit];  //自适应文本内容大小
    return label.frame.size.height * 1.7;
}
{%endcodeblock%}
{% codeblock lang:swift %}
override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat
{
    let label = UILabel.init(frame: CGRect.init(x: 0, y: 0, width: FLT_MAX, height: FLT_MAX))
    label.text = "test"
    let font = UIFont.preferredFont(forTextStyle: .headline)
    label.font = font
    label.sizeToFit()
    return label.frame.size.height * 1.7
}
{% endcodeblock %}

以上代码创建了一个共享的——或者说静态的——**UILabel**实例，设定它的字体和表中单元格内文本字体一致。然后调用它的`sizeToFit`方法，使这个`label`的`frame`恰好能放得下它的内容文字, 然后把这个`label`的高度乘个`1.7`作为表内单元格高度。  
Build并运行app，修改字体大小设置，行高也会随着字体大小的变化而变化。 如下图所示：  
![image](/images/TableViewAdaptsHeights.png)  

### 凸版印刷效果（Letterpress effects）  
凸版印刷效果（Letterpress effects）给文字加上精致的阴影和高光是文字看上去有一定立体感——就好像轻轻嵌入屏幕里一样。  
> <font size=3>注: 使用“凸版印刷（letterpress）”这一印刷术语是向早期印刷业的致敬。所谓凸版印刷，就是将涂上油墨的图文凸版嵌在印版上，然后在纸面上按压就把图文凸版上的油墨转移到纸面上了——纸面受力在文字边缘形成好看的突起。现在这一工艺已广泛被数码打印所取代。</font>  

打开NotesListViewController.m 将`tableView:cellForRowAtIndexPath: `方法中的代码用以下代码替换:  
{%codeblock lang:objc%}
    static NSString *CellIdentifier = @"Cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier 
                                                            forIndexPath:indexPath];
    Note* note = [self notes][indexPath.row];

    UIFont* font = [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline];

    UIColor* textColor = [UIColor colorWithRed:0.175f green:0.458f blue:0.831f alpha:1.0f];

    NSDictionary *attrs = @{ NSForegroundColorAttributeName : textColor,
                                        NSFontAttributeName : font,
                                  NSTextEffectAttributeName : NSTextEffectLetterpressStyle};

    NSAttributedString* attrString = [[NSAttributedString alloc]
                                           initWithString:note.title
                                               attributes:attrs];
    cell.textLabel.attributedText = attrString;
    return cell;
{%endcodeblock%}
{% codeblock lang:swift %}
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
{    
    let cell = tableView.dequeueReusableCell(withIdentifier: "noteListCell", for: indexPath)
    // Configure the cell...
    let note = notes[indexPath.row] as! NoteModel
    //cell.textLabel?.text = note.title
    let font = UIFont.preferredFont(forTextStyle: .headline)
    let textColor = UIColor.init(red: 0.175, green: 0.458, blue: 0.831, alpha: 1.0)
    //字体凸版印刷效果
    let store:[String:Any] = [NSForegroundColorAttributeName:textColor,
    NSFontAttributeName:font,
    NSTextEffectAttributeName:NSTextEffectLetterpressStyle]
    cell.textLabel?.attributedText = NSAttributedString.init(string: note.title, attributes: store)
    return cell
}
{% endcodeblock %}

上面的代码为单元格的标题创建了一个使用了凸版印刷效果的**`NSAttributedString`**。  <p>
Build并运行app， 表格将显示凸版印刷效果，如下图所示：  
![image](/images/Letterpress.png)  
凸版印刷效果是很精巧——但是并不表示你可以随意过度使用它。视觉特效能让文字看上去更有趣，但并不表示一定能让你的文字更清晰易读。

### 环绕路径（Exclusion paths）
文字环绕图片或其它内容分布是大多数文字处理软件的标准特性之一。`Text Kit`允许你通过环绕路径（`exclusion paths`）将文字按照复杂路径和形状分布。  

在便笺右上角添加一个曲线形视图，告知用户便笺创建的日期：   
* 首先添加一个视图  
* 创建一个环绕路径，使文字按照这个路径分布。 

#### 添加视图
打开 `NoteEditorViewController.m` 在顶部的`imports`和接口实现中添加变量加入以下代码：
{%codeblock lang:objc%}
#import "TimeIndicatorView.h"

@implementation NoteEditorViewController
{
    TimeIndicatorView* _timeView;
}
{%endcodeblock%}
实例化这个用以显示文本创建日期的视图实例，并把它作为一个子视图添加进去
在NoteEditorViewController.m的`viewDidLoad`方法的最后添加以下代码：
{%codeblock objc lang:objc%}
_timeView = [[TimeIndicatorView alloc] initWithDate:_note.timestamp];
[self.view addSubview:_timeView];
{%endcodeblock%}

#### 设置视图位置及自动适应布局：`viewDidLayoutSubviews`
当**NoteEditor**视图的控件调用系统方法`viewDidLayoutSubviews`方法，对子视图进行布局时，`TimeIndicatorView`作为子控件也需要有相应的变化。  
在控件接收到文本内容的尺寸发生了变化的时候调用`updateTimeIndicatorFrame`： 
1. 第一调用`updateSize`来设定`_timeView`的尺寸  
2. 第二将`_timeView`放在右上角  
在NoteEditorViewController.m 的最后添加如下代码：
{%codeblock objc lang:objc%}
- (void)viewDidLayoutSubviews 
{
    [self updateTimeIndicatorFrame];
}
- (void)updateTimeIndicatorFrame
{
    [_timeView updateSize];
    _timeView.frame = CGRectOffset(_timeView.frame,
                          self.view.frame.size.width - _timeView.frame.size.width, 0.0);
}
{%endcodeblock%}
{% codeblock swift lang:swift %}
//视图的控件调用viewDidLayoutSubviews对子视图进行布局时，TimeIndicatorView作为子控件也需要有相应的变化。
override func viewDidLayoutSubviews() 
{
    updateTimeIndicatorFrame()
}

func updateTimeIndicatorFrame() 
{
    //第一调用updateSize来设定_timeView的尺寸
    timeIndicatorView.updateSize()
    //通过偏移frame参数，将timeIndicatorView放在右上角
    timeIndicatorView.frame = timeIndicatorView.frame.offsetBy(dx: ibTextView.frame.width - timeIndicatorView.frame.width,
                                                               dy: 0.0)
}
{% endcodeblock %}

#### 响应系统偏好设置字体样式
修改`NoteEditorViewController.m`中`preferredContentSizeChanged:`方法如下：
{%codeblock lang:objc%}
- (void)preferredContentSizeChanged:(NSNotification *)n 
{
    self.textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
    [self updateTimeIndicatorFrame];
}
{%endcodeblock%}
Build并运行app，点击选择一个便笺，日期显示视图将出现在右上角，如下图所示：  
![修改设备中文本大小设置，这个视图也将自动调整到相应的合适尺寸](/images/TimIndicator.png)    

### 文本环绕视图
1. 根据日期视图`_timeView`创建基于贝赛尔路径的环绕路径
2. 设置文本容器的环绕路径：使用文本容器的exclusionPaths属性指定。它是一个UIBezierPath数组类型。

#### curvePathWithOrigin:创建文本容器的赛尔路径
根据日期视图`_timeView`创建基于贝赛尔路径的环绕路径 
添加`curvePathWithOrigin:`方法，定义文本遵循的环绕路径：
{%codeblock objc lang:objc%}
- (UIBezierPath *)curvePathWithOrigin:(CGPoint)origin
{
    return [UIBezierPath bezierPathWithArcCenter:origin
                                          radius: [self radiusToSurroundFrame:_label.frame]
                                      startAngle: -180.0f
                                        endAngle: 180.0f
                                       clockwise: YES];
}
{%endcodeblock%}
{% codeblock swift lang:swift %}
func curvePathWithOrigin(origin:CGPoint)->UIBezierPath
{
    //画弧形
    let path = UIBezierPath.init(arcCenter: origin,
    radius: radiusToSurroundFrame(frame: timeLabel.frame),
                             startAngle: -180,                //-180.0
                               endAngle: 180.0,               //CGFloat(M_PI * 2),   //180.0
                              clockwise: true)
    //        UIColor.blueColor().set()
    //        path.fill()
    //        UIColor.blueColor().set()
    return path
}

{% endcodeblock %}

#### 设置文本容器的环绕路径：exclusionPaths
`exclusionPaths`是`NSArray`类型，因此一个文本容器是可以支持多个环绕路径，文本**环绕路径**发生改变后会通知文本管理器，然后**环绕路径**的变化就可以动态地，甚至是动画式地体现到文本上！ 
在`updateTimeIndicatorFrame`方法实现的最后面添加如下代码：
{%codeblock objc lang:objc%}
{
    UIBezierPath* exclusionPath = [_timeView curvePathWithOrigin:_timeView.center];
    _textView.textContainer.exclusionPaths  = @[exclusionPath];
}
{%endcodeblock%} 
{%codeblock swift lang:swift %}
{
    let exclusionPath = timeIndicatorView.curvePathWithOrigin(origin: timeIndicatorView.center)
    ibTextView.textContainer.exclusionPaths = [exclusionPath]
}
{%endcodeblock%} 
Build并运行app，选择一个便笺项，如下图所示：  
![实现文本环绕效果](/images/ExclusionPath.png)  
  
## 动态文本格式及存储（Dynamic text formatting and storage）
你已经看到了`Text Kit`可以根据用户设置的字体大小动态地调整字体。但是如果字体也可以根据实际的文字本身来进行动态更新是不是会更酷呢？  
实现类似markdown语法的效果：  
* 把波浪线(~)之间的文本变为艺术字体  
* 把下划线(_)之间的文本变为斜体  
* 为破折号(-)之间的文本添加删除线  
* 把字母全部大写的单词变为红色  
![利用Text Kit framework来实现的效果](/images/DynamicTextExample.png)   

### Text Kit文本系统工作机制
`Text Kit 堆栈`存储、处理以及显示文本：  
![image](/images/TextKitStack-443x320.png)  
当你创建`UITextView`, `UILabel` or `UITextField`的时候，Apple系统自动在后台帮你创建了这些类。你可以使用这些默认的实现或者是自定义一部分，以便达到想要的效果。
* **`NSTextStorage`文本存储器**: 作为一个`NSMutableAttributedString`的子类，动态处理的文本可以通过`attributedString`的方式存储，并且将文本内容的任何变化都通知给布局管理器。可以自定义`NSTextStorage`的子类，当文本发生变化时，动态地对文本属性做出相应改变。  
* **`NSLayoutManager`布局引擎**: 获取存储的文本并经过修饰处理再显示在屏幕上； 
* **`NSTextContainer`文本容器**: 描述所要处理的文本在屏幕上的位置信息。每一个文本容器都有一个关联的`UITextView`. 可以创建 `NSTextContainer`的子类来定义**一个复杂的形状**，然后在这个形状内处理文本。  

### `NSTextStorage`文本存储器动态添加文本属性
1. 需要创建一个`NSTextStorage`的子类，用以在用户输入文本的时候，动态地添加文本属性。
2. 将`UITextView`的默认文本存储器,用自定义的实现替换掉。

#### 创建文本存储器NSTextStorage子类
新建**`NSTextStorage`**的子类，类命名为**`SyntaxHighlightTextStorage`** 
打开**SyntaxHighlightTextStorage.m**并添加实例变量并初始化：
{%codeblock lang:objc%}
#import "SyntaxHighlightTextStorage.h"
@implementation SyntaxHighlightTextStorage
{
    NSMutableAttributedString *	_backingStore;
}

- (id)init
{
    if (self = [super init]) 
    {
        _backingStore = [NSMutableAttributedString new];
    }
    return self;
}
@end
{%endcodeblock%}

{% codeblock swift lang:swift %}
class SyntaxHighlightTextStorage: NSTextStorage
{
    //文本存储器子类必须提供它自己的“数据持久化层”。
    var backingStore = NSMutableAttributedString()
}

{% endcodeblock %}

#### 重载文本存储器的数据持久化层方法
要使用**`NSMutableAttributedString`**作为“后台存储” (后面会详细讲解)，文本存储器子类必须提供它自己的“数据持久化层”： 
{%codeblock lang:objc%}
- (NSString *)string
{
    return [_backingStore string];
}

- (NSDictionary *)attributesAtIndex:(NSUInteger)location
                     effectiveRange:(NSRangePointer)range
{
    return [_backingStore attributesAtIndex:location
                             effectiveRange:range];
}
{%endcodeblock%}
{% codeblock swift lang:swift %}
override var string: String
{
    return backingStore.string
}

override func attributes(at location: Int, effectiveRange range: NSRangePointer?) -> [String : Any]
{
    if range == nil 
    {
        return [:]
    }
    //print("backingStore:location\(location),effectiveRange:\(range!)")
    return backingStore.attributes(at: location, effectiveRange: range!)
}

{% endcodeblock %}
上面两个方法直接把任务代理给了后台存储。  

#### 重载编辑文本时通知布局管理器的方法
同样的，这些方法也是把任务代理给后台存储。它们通过调用`beginEditing` / `edited` / `endEditing`这些方法来完成一些编辑任务。这样做是为了在编辑发生后让文本存储器的类通知相关的布局管理器。
最后，还在这个文件中，重载以下方法：
{%codeblock lang:objc%}
- (void)replaceCharactersInRange:(NSRange)range withString:(NSString *)str
{
    NSLog(@"replaceCharactersInRange:%@ withString:%@", NSStringFromRange(range), str);

    [self beginEditing];
    [_backingStore replaceCharactersInRange:range withString:str];
    [self edited:NSTextStorageEditedCharacters | NSTextStorageEditedAttributes
              range:range
     changeInLength:str.length - range.length];
    [self endEditing];
}

- (void)setAttributes:(NSDictionary *)attrs range:(NSRange)range
{
    NSLog(@"setAttributes:%@ range:%@", attrs, NSStringFromRange(range));

    [self beginEditing];
    [_backingStore setAttributes:attrs range:range];
    [self edited:NSTextStorageEditedAttributes range:range changeInLength:0];
    [self endEditing];
}
{%endcodeblock%}

{% codeblock swift lang:swift %}
override func replaceCharacters(in range: NSRange, with str: String)
{
    print("replaceCharactersInRange:\(NSStringFromRange(range)) withString:\(str)")
    beginEditing()
    backingStore.replaceCharacters(in: range, with: str)
    edited([.editedAttributes,.editedCharacters], range: range, changeInLength: str.utf16.count - range.length)
    endEditing()
}

override func setAttributes(_ attrs: [String : Any]?, range: NSRange)
{
    //Sets the attributes for the characters in the specified range to the specified attributes.
    print("setAttributes:\(attrs!) range:\(NSStringFromRange(range))")
    beginEditing()
    backingStore.setAttributes(attrs!, range: range)
    edited(.editedAttributes, range: range, changeInLength: 0)
    endEditing()
}
{% endcodeblock %}

#### 类族介绍
类族是Apple的framework中广泛用到的一种设计模式。类族就是抽象工厂模式的实现，无需指定具体的类就可以为创建一族相关或从属的对象提供一个公共接口。一些我们很熟悉的类`NSArray`和`NSNumber`类似的就是一族类的公共接口。

上例中`NSTextStorage`文本存储器就是一个类族的公共接口，需要大量代码来创建文本存储器的子类。在扩展功能时，通过创建子类及重载几个方法之外，有些特定需求是要自己实现的，比方`attributedString`数据的后台存储。
  
Apple使用类族来封装同一个公共`抽象超类`下的私有具体子类，`抽象超类`声明了客户在创建`私有子类实例`时必须要实现的方法。客户是完全无法知道工厂正在用哪一个私有类，它只和公共接口相互协作。  
使用类族当然可以简化接口，使学习和使用类更加容易，但是必须要需要指出的是要在功能扩展和接口简化之间达到平衡。创建一个类族的抽象超类的定制子类也常常是非常难的。 

### 创建UITextView使用自定义Text Kit堆栈
现在有了一个自定义的`NSTextStorage`，还需创建一个`UITextView`来使用它。 

#### storyboard创建UITextView时，Text Kit组件只读问题
从**storyboard**编辑器实例化`UITextView`会自动创建**`NSTextStorage`**, **`NSLayoutManager`**和**`NSTextContainer`** (例如**Text Kit**堆栈)实例以及所有的这三个只读属性。  
虽然没有办法从**storyboard**编辑器中改变这种设定，但可以手动编程创建`UITextView`和**Text Kit**堆栈。  

#### 在UITextView中使用自定义的SyntaxHighlightTextStorage

##### 清理IB相关设置
* 在IB中打开**Main.storyboard** 找到**NoteEditorViewController**。 删除`UITextView`实例。
然后，打开**NoteEditorViewController.m**删除**UITextView outlet**。
既然不再为文本视图使用`IBOutlet`，而是要编程添加，所以也就不需要这些代码了。
从`viewDidLoad` 方法中删除以下代码：
{%codeblock lang:objc%}
self.textView.text = self.note.contents;
self.textView.delegate = self;
self.textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
{%endcodeblock%}

##### 手动创建`UITextView`和Text Kit堆栈
* 在**NoteEditorViewController.m**最上面，添加下面一行代码:
{%codeblock objc lang:objc %}
#import "SyntaxHighlightTextStorage.h"
{%endcodeblock%}
在NoteEditorViewController.m中`TimeIndicatorView`实例变量后面紧接着添加以下代码：
{%codeblock lang:objc%}
SyntaxHighlightTextStorage* _textStorage;
UITextView* _textView;
{%endcodeblock%}
文本存储器子类有两个实例变量，还有一个文本视图稍后需要添加。  

##### 创建`Text Kit`堆栈
* 创建自定义的`NSTextStorage`文本存储器实例，一个用来承载便笺内容的`NSAttributedString`  
* 创建一个`NSLayoutManager`布局管理器，并添加到文本存储器。
* 创建一个`NSTextContainer`文本容器，并添加到布局管理器。然后把布局管理器和文本存储器联系起来  
* 最后用你自定义的文本容器和代理组创建实际的文本视图，  并把文本视图添加为子视图  
在NoteEditorViewController.m中，添加下面方法：
{%codeblock lang:objc%}
- (void)createTextView
{
    // 1. Create the text storage that backs the editor
    NSDictionary* attrs = @{NSFontAttributeName:
        [UIFont preferredFontForTextStyle:UIFontTextStyleBody]};
    NSAttributedString* attrString = [[NSAttributedString alloc]
                                   initWithString:_note.contents
                                       attributes:attrs];
    _textStorage = [SyntaxHighlightTextStorage new];
    [_textStorage appendAttributedString:attrString];

    CGRect newTextViewRect = self.view.bounds;

    // 2. Create the layout manager
    NSLayoutManager *layoutManager = [[NSLayoutManager alloc] init];

    // 3. Create a text container
    CGSize containerSize = CGSizeMake(newTextViewRect.size.width,  CGFLOAT_MAX);
    NSTextContainer *container = [[NSTextContainer alloc] initWithSize:containerSize];
    container.widthTracksTextView = YES;
    [layoutManager addTextContainer:container];
    [_textStorage addLayoutManager:layoutManager];

    // 4. Create a UITextView
    _textView = [[UITextView alloc] initWithFrame:newTextViewRect
                                    textContainer:container];
    _textView.delegate = self;
    [self.view addSubview:_textView];
}
{%endcodeblock%}
{% codeblock swift lang:swift %}
//创建文本区域
func createTextView()
{
    // 1. Create the text storage that backs the editor
    let bodyFont = UIFont.preferredFont(forTextStyle: UIFontTextStyle.body)
    let attrs = [NSFontAttributeName:bodyFont]
    let attrString = NSAttributedString(string: note.contents,attributes: attrs)
    textStorage = SyntaxHighlightTextStorage()
    textStorage.append(attrString)

    // --------使用Storyboard声明TextView时,只需一行，可惜为只读属性----------
    textStorage.addLayoutManager(ibTextView.layoutManager)

    /**--------使用代码声明TextView时，4步骤----------
    let newTextViewRect = view.bounds

    // 2. Create the layout manager
    let layoutManager = NSLayoutManager()

    // 3. Create a text container
    //文本容器的宽度会自动匹配视图的宽度，但是它的高度是无限高的——或者说无限接近于CGFloat.max，它的值可以是无限大。
    let containerSize = CGSize.init(width: newTextViewRect.size.width,
    height: CGFloat.greatestFiniteMagnitude)

    let container = NSTextContainer.init(size: containerSize)
    //A Boolean that controls whether the receiver adjusts the width of its bounding rectangle when its text view is resized.
    container.widthTracksTextView = true
    //
    layoutManager.addTextContainer(container)
    textStorage.addLayoutManager(layoutManager)

    // 4. Create a UITextView
    textView = UITextView()//.init(frame: newTextViewRect, textContainer: container)
    textView.isScrollEnabled = true
    textView.delegate = self
    view.addSubview(textView)
    */
}
{% endcodeblock %}

现在回顾之前那个图表所展示的四个关键类(文本存储器`storage`, 布局管理器`layout manager`, 文本容器`container` 和文本视图`textView`)之间的关系，是不是觉得理解起来容易多了。  
![image](/images/TextKitStack-443x320.png)
><font size=3>注意:文本容器的宽度会自动匹配视图的宽度，但是它的高度是无限高的——或者说无限接近于`CGFLOAT_MAX`，它的值可以是无限大。不管怎么说，它的高度足够让`UITextView`上下滚动以容纳很长的文本。</font>    

在`viewDidLoad`方法中调用超类的`viewDidLoad`方法的语句后面添加以下一行代码：
{%codeblock lang:objc%}
[self createTextView];
{%endcodeblock%}
然后修改`preferredContentSizeChanged`的第一行代码为：
{%codeblock lang:objc%}
_textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
{%endcodeblock%}

##### 自定义视图实现在`storyboard`中自动布局约束的效果
用自定义的实例变量来替换掉旧的`outlet`属性。自定义视图不会自动继承`storyboard`中的布局约束组的规则。当设备方向变化后，视图的边界是不会自动随之改变的，这样就需要自己来编程设定视图边界。  

可以在`viewDidLayoutSubviews`方法的最后添加以下代码来实现：
{%codeblock lang:objc%}
_textView.frame = self.view.bounds;
{%endcodeblock%}
Build并运行app，打开一个便笺项，在Xcode控制台上有`SyntaxHighlightTextStorage`生成的运行日志，用来告诉你这些文本处理的代码确实被调用：  
![image](/images/LogMessages-480x266.png)
看来你的文本解析器的基础非常可靠了 —— 那现在来添加动态格式。

### 通过正则修改文本存储器的动态格式（Dynamic formatting）
接下来将对你的自定义文本存储器进行修改以将＊星号符之间的文本＊变为黑体：

#### `processEditing`：将文本的变化通知给布局管理器
`processEditing` 将文本的变化通知给布局管理器。它也为文本编辑之后的处理提供便利。
打开**SyntaxHighlightTextStorage.m** 添加以下方法：
{%codeblock lang:objc%}
-(void)processEditing
{
    [self performReplacementsForRange:[self editedRange]];
    [super processEditing];
}
{%endcodeblock%}

#### 
NSUnionRange：在range1和range2之间比较，如果一个range完全包含在另一个range内，则返回较大的range
上面的代码拓展了受黑体格式类型影响的文本范围。因为`changedRange`一般只是作用到单独的一个字符； 而`lineRangeForRange` 则扩展到一整行
在 `processEditing`方法之后紧接着添加以下代码：
{%codeblock lang:objc%}
- (void)performReplacementsForRange:(NSRange)changedRange
{
    NSRange extendedRange = NSUnionRange(changedRange, [[_backingStore string]
                             lineRangeForRange:NSMakeRange(changedRange.location, 0)]);
    extendedRange = NSUnionRange(changedRange, [[_backingStore string] 
                          lineRangeForRange:NSMakeRange(NSMaxRange(changedRange), 0)]);
    [self applyStylesToRange:extendedRange];
}
{%endcodeblock%}
{% codeblock swift lang:swift %}
//在指定的区域中进行替换
func performReplacementsForRange(_ changedRange:NSRange)
{
    //定位正在编辑文本的位置区间
    let locationRange = NSMakeRange(changedRange.location, 0)
    //定位到文本当前行的位置区间
    let range1 = (backingStore.string as NSString).lineRange(for: locationRange)
    
    //扩展范围
    var extendedRange = NSUnionRange(changedRange, range1)

    let maxRange = NSMakeRange(NSMaxRange(changedRange), 0)

    let range2 = (backingStore.string as NSString).lineRange(for: maxRange)
    extendedRange = NSUnionRange(changedRange, range2)

    print("在指定的区域中进行替换:\(extendedRange)")
    applyStylesToRange(searchRange: extendedRange)
}

{% endcodeblock %}

在 `performReplacementsForRange`方法之后紧接着添加以下代码：
{%codeblock lang:objc%}
- (void)applyStylesToRange:(NSRange)searchRange
{
    // 1. create some fonts
    UIFontDescriptor* fontDescriptor = [UIFontDescriptor
                             preferredFontDescriptorWithTextStyle:UIFontTextStyleBody];
    UIFontDescriptor* boldFontDescriptor = [fontDescriptor
                           fontDescriptorWithSymbolicTraits:UIFontDescriptorTraitBold];
    UIFont* boldFont =  [UIFont fontWithDescriptor:boldFontDescriptor size: 0.0];
    UIFont* normalFont =  [UIFont preferredFontForTextStyle:UIFontTextStyleBody];

    // 2. match items surrounded by asterisks
    NSString* regexStr = @"(*w+(sw+)**)s";
    NSRegularExpression* regex = [NSRegularExpression
                                   regularExpressionWithPattern:regexStr
                                                        options:0
                                                          error:nil];

    NSDictionary* boldAttributes = @{ NSFontAttributeName : boldFont };
    NSDictionary* normalAttributes = @{ NSFontAttributeName : normalFont };

    // 3. iterate over each match, making the text bold
    [regex enumerateMatchesInString:[_backingStore string]
              options:0
                range:searchRange
           usingBlock:^(NSTextCheckingResult *match,
                        NSMatchingFlags flags,
                        BOOL *stop){

        NSRange matchRange = [match rangeAtIndex:1];
        [self addAttributes:boldAttributes range:matchRange];

        // 4. reset the style to the original
        if (NSMaxRange(matchRange)+1 < self.length) {
            [self addAttributes:normalAttributes
                range:NSMakeRange(NSMaxRange(matchRange)+1, 1)];
        }
    }];
}
{%endcodeblock%}
上面的代码有以下作用：  

  1. 创建一个粗体及一个正常字体并使用字体描述器（**Font descriptors**）来格式化文本。字体描述器能使你无需对字体手动编码来设置字体和样式。  
  2. 创建一个正则表达式来定位星号符包围的文本。例如，在字符串“iOS 7 is \*awesome\*”中，存储在regExStr中的正则表达式将会匹配并返回文本“\*awesome\*”。
  3. 对正则表达式匹配到并返回的文本进行枚举并添加粗体属性。  

将后一个星号符之后的文本都重置为“常规”样式。以保证添加在后一个星号符之后的文本不被粗体风格所影响。
> <font size=3>注： 字体描述器（**Font descriptors**）是一种描述性语言，它使你可以通过设置属性来修改字体，或者无需初始化`UIFont`实例便可获取字体规格的细节。</font>    

Build并运行app；向便笺中输入文本，并将其中一个词用星号符包围。这个词将会自动变为黑体，如下面截图所示：  
![image](/images/BoldText.png)  

##进一步添加样式
为限定文本添加风格的基本原则很简单：**使用正则表达式来寻找和替换限定字符，然后用applyStylesToRange来设置想要的文本样式即可。**  
在SyntaxHighlightTextStorage.m中添加以下实例变量：
{%codeblock lang:objc%}
- (void) createHighlightPatterns {
    UIFontDescriptor *scriptFontDescriptor =
      [UIFontDescriptor fontDescriptorWithFontAttributes:
          @{UIFontDescriptorFamilyAttribute: @"Zapfino"}];

    // 1. base our script font on the preferred body font size
    UIFontDescriptor* bodyFontDescriptor = [UIFontDescriptor
      preferredFontDescriptorWithTextStyle:UIFontTextStyleBody];
    NSNumber* bodyFontSize = bodyFontDescriptor.
                  fontAttributes[UIFontDescriptorSizeAttribute];
    UIFont* scriptFont = [UIFont
              fontWithDescriptor:scriptFontDescriptor size:[bodyFontSize floatValue]];

    // 2. create the attributes
    NSDictionary* boldAttributes = [self
     createAttributesForFontStyle:UIFontTextStyleBody
                        withTrait:UIFontDescriptorTraitBold];
    NSDictionary* italicAttributes = [self
     createAttributesForFontStyle:UIFontTextStyleBody
                        withTrait:UIFontDescriptorTraitItalic];
    NSDictionary* strikeThroughAttributes = @{ NSStrikethroughStyleAttributeName : @1};
    NSDictionary* scriptAttributes = @{ NSFontAttributeName : scriptFont};
    NSDictionary* redTextAttributes =
                          @{ NSForegroundColorAttributeName : [UIColor redColor]};

    // construct a dictionary of replacements based on regexes
    _replacements = @{
              @"(\*w+(sw+)\*\*)s" : boldAttributes,
              @"(_w+(sw+)\*_)s" : italicAttributes,
              @"([0-9]+.)s" : boldAttributes,
              @"(-w+(sw+)\*-)s" : strikeThroughAttributes,
              @"(~w+(sw+)\*~)s" : scriptAttributes,
              @"s([A-Z]{2,})s" : redTextAttributes};
}
{%endcodeblock%}
  
这个方法的作用：

   1. 首先，它使用Zapfino字体来创建了“`script`”风格。**Font descriptors**会决定当前正文的首选字体，以保证`script`不会影响到用户的字体大小设置。  
   2. 然后，它会为每种匹配的字体样式构造各个属性。你稍后将用到 **`createAttributesForFontStyle:withTrait:`**。
   3. 最后，它将创建一个`NSDictionary`并将正则表达式映射到上面声明的属性上。

如果你对正则表达式不是非常熟悉，上面的的dictionary对你来说可能很陌生。但是，如果你一点一点仔细分析它其中包含的正则表达式，其实不用很费力就能理解了。  

以上面实现的第一个正则表达式为例，它的工作是匹配星号符包围的文本：  
(\*w+(sw+)\*\*)s  
上面两个两个相连的斜杠，其中一个是用来将Objective-C中的特殊字符转义成实体字符。去掉用来转义的斜杠，来看下这个正则表达式的核心部分：  
(\*w+(sw+)\*\*)s  
现在，逐步来分析这个正则表达式：  
{%codeblock lang:js%}
(*	 	 	  ——  匹配星号符  
w+   	 	 —— 后接一个或多个 “word”式 字符串  
(sw+)*	   —— 后接零个或多组空格然后再接 “word” 式字符串  
*)   	  —— 后接星号符  
s   	  —— 以空格结尾  
{%endcodeblock%}
> <font size=3>注：如果你想对正则表达式有更多了解，请参考 [NSRegularExpression tutorial and cheat sheet](http://www.raywenderlich.com/30288/nsregularexpression-tutorial-and-cheat-sheet).</font>  

现在你需要调用`createHighlightPatterns：`
将**SyntaxHighlightTextStorage.m** 中的`init`方法更新如下：
{%codeblock lang:objc%}
- (id)init
{
    if (self = [super init]) {
        _backingStore = [NSMutableAttributedString new];
        [self createHighlightPatterns];
    }
    return self;
}
{%endcodeblock%}

在SyntaxHighlightTextStorage.m方法中添加以下代码：
{%codeblock lang:objc%}
- (NSDictionary*)createAttributesForFontStyle:(NSString*)style
                                    withTrait:(uint32_t)trait {
    UIFontDescriptor *fontDescriptor = [UIFontDescriptor
                               preferredFontDescriptorWithTextStyle:UIFontTextStyleBody];

    UIFontDescriptor *descriptorWithTrait = [fontDescriptor
                                    fontDescriptorWithSymbolicTraits:trait];

    UIFont* font =  [UIFont fontWithDescriptor:descriptorWithTrait size: 0.0];
    return @{ NSFontAttributeName : font };
}
{%endcodeblock%}

上面的代码作用是将提供的字体样式作用到正文字体上。它给`fontWithDescriptor:size:` 提供的`size`值为0，这样做会迫使`UIFont`返回用户设置的字体大小。
