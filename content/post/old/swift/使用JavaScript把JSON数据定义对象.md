---
title: 使用JavaScript把JSON数据定义对象
date: 2017-02-12 14:41:35
updated: 2018-10-15 17:22:37
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
---

## 将JSON数据序列化为代码实例
1. 定义一个继承自 `JSExport` 子协议 `PersonJSExports`
2. 定义一个遵守`PersonJSExports`协议的`Person`数据模
3. 使用 `JavaScript` 把`JSON`转为`类`并实例化`对象`
都有一个完整的 `JVM` 在那儿了，谁还需要 `NSJSONSerialization`？

## JSExport语言互通协议
`JavaScript`可以脱离`prototype继承`完全用`JSON`来定义对象，但是`Objective-C`编程不能脱离`类`和`继承`。所以`JavaScriptCore`就提供了`JSExport`作为两种语言的互通协议。
`JSExport`中没有预定义任何方法，但是所有继承`JSExport`的协议中定义的方法即协议方法，都可以在`JSContext`中被调用。
## JSExportAs 宏: 指定方法在JS中调用的名称
对于多参数的方法，`JavaScriptCore`的转换方式将`Objective-C`的方法每个部分都合并在一起，冒号后的字母变为大写并移除冒号。比如下边协议中的方法，在JavaScript调用就是：doFooWithBar(foo, bar);
{% codeblock lang:objc %}
@protocol MultiArgs <JSExport>
- (void)doFoo:(id)foo withBar:(id)bar;
@end
{% endcodeblock %}
如果希望方法在JavaScript中有一个比较短的名字，就需要用的JSExport.h中提供的宏：
{% codeblock JSExport.h lang:objc %}
JSExportAs(PropertyName, Selector)
{% endcodeblock %}

{% codeblock JSExportAs的官方宏定义 lang:objc xcdoc://?url=developer.apple.com/library/etc/redirect/xcode/ios/1151/documentation/JavaScriptCore/Reference/JSExport_Ref/index.html  %}
#define JSExportAs(PropertyName, Selector) \
@optional Selector __JS_EXPORT_AS__##PropertyName:(id)argument; @required Selector
#endif
{% endcodeblock %}
如 `setX:Y:Z`方法，我们可以给他重命名，让 JS 中通过 `set3D(x,y,z)` 来调用
{% codeblock 使用方法 lang:objc %}
JSExportAs(set3D,
- (void)setX:(id)x Y:(id)y Z:(id)z
);
//调用
set3D(x,y,z)
{% endcodeblock %}

### 定义PersonJSExports协议和create协议方法（类方法） 
`Person 类`实现了` PersonJSExports 协议`，该协议规定哪些属性可以在 `JavaScript`中可用。
在`JavaScript`语境中，不能像：`var person = new Person()`来初始化实例，可以在定义`PersonJSExports`协议方法时，添加一个协议方法来弥补这一点。见下例`create...`类方法：
{% codeblock  lang:swift %}
// Custom protocol must be declared with `@objc`
@objc protocol PersonJSExports : JSExport {
    //属性
    var firstName: String { get set }
    var lastName: String { get set }
    var birthYear: NSNumber? { get set }
    //方法
    func getFullName() -> String

    /// 在JavaScript中调用这个类方法
    class func createWithFirstName(firstName: String, lastName: String) -> Person
}

### 定义Person模型
定义一个遵守`PersonJSExports`协议的`Person`数据模
// Custom class must inherit from `NSObject`
@objc class Person : NSObject, PersonJSExports {
    // JS协议属性必须声明为`dynamic`
    dynamic var firstName: String
    dynamic var lastName: String
    dynamic var birthYear: NSNumber?

    init(firstName: String, lastName: String)   
    {
        self.firstName = firstName
        self.lastName = lastName
    }
    //JS协议类方法
    class func createWithFirstName(firstName: String, lastName: String) -> Person 
    {
        return Person(firstName: firstName, lastName: lastName)
    }
    //JS协议方法
    func getFullName() -> String 
    {
        return "\(firstName) \(lastName)"
    }
}
{% endcodeblock %}

### JSContext 配置
之前，我们可以用我们已经创建的 Person 类，我们需要将其导出到 `JavaScript` 环境。我们也将借此导入[Mustache JS library](http://mustache.github.io)，我们将应用模板到我们的 Person 对象。
{% codeblock lang:swift %}
// export Person class，JS中以该`Person标示符`作为类名使用
context.setObject(Person.self, forKeyedSubscript: "Person")

// load Mustache.js
if let mustacheJSString = String(contentsOfFile:..., encoding:NSUTF8StringEncoding, error:nil) 
{
    context.evaluateScript(mustacheJSString)
}
{% endcodeblock %}

### JavaScript 数据和进程
下面就来看看我们简单的 JSON 例子，这段代码将创建新的 Person 实例。
数据：
{% codeblock Persons.json lang:json %}
[
    { "first": "Grace",     "last": "Hopper",   "year": 1906 },
    { "first": "Ada",       "last": "Lovelace", "year": 1815 },
    { "first": "Margaret",  "last": "Hamilton", "year": 1936 }
]
{% endcodeblock %}
创建新的 Person 实例：
{% codeblock loadPeople.js lang:js %}
var loadPeopleFromJSON = function(jsonString) 
{
    var data = JSON.parse(jsonString);
    var people = [];
    for (i = 0; i < data.length; i++) 
    {
        //在swift中的js协议方法：`createWithFirstName:lastName:`
        var person = Person.createWithFirstNameLastName(data[i].first, data[i].last);
        person.birthYear = data[i].year;
        //`push:`添加到数组中
        people.push(person);
    }
    //返回该对象
    return people;
}
{% endcodeblock %}
`JSContext`加载装换`loadPeople.js`脚本
加载js脚本之后，`loadPeopleFromJSON`即可作为下标被`context`调用该方法:
{% codeblock 加载loadPeople.js lang:swift %}
// load loadPeople.js
if let loadPeople = String(contentsOfFile:..., encoding:NSUTF8StringEncoding, error:nil) 
{
    //加载js脚本之后，`loadPeopleFromJSON`即可作为下标被context调用该方法
    context.evaluateScript(loadPeople)
}
{% endcodeblock %}

>注意：JavaScriptCore 转换的 Objective-C / Swift 方法名是 JavaScript 兼容的。由于 JavaScript 没有参数 名称，任何外部参数名称都会被转换为驼峰形式并且附加到函数名后。在这个例子中，Objective-C 的方法 createWithFirstName:lastName: 变成了在JavaScript中的 createWithFirstNameLastName()。

### 使用Mustache 模板 渲染
Mustache 是一个很强大的 template 引擎，可以通过解析 json 来绑定并渲染占位符。如果你做过一些前端开发的话，会知道这是一种很常用的 HTML 绑定 Model 的做法，GRMustache.swift 是这个框架的 Swift 实现。
[mustache模板引擎](http://blog.csdn.net/kevin_luan/article/details/46485561)
[Mustache 的 Swift 语言实现版本](https://github.com/BjornRuud/Swiftache)
mustache的特点就是很语法很简单，主要语法如下:
    1. {{ name }} 打印变量，默认是escape过的，如果不要escape,用3个分隔符 {{{ name }}}，或者用 {{ &name }}，这个和分隔符无关
    2. {{#person}}…{{/person}} 区块，4种方式
        person 是真假值，决定是否输出
        person 是list of array，会循环展开 for x in person:section.render('xxx)
        person 是匿名函数/object, 区块包裹的html 会作为参数传递进去
        person 是dict，直接打印 dict[key]
    3. {{^person}}…{{/person}，反向区块
    4. {{！name }} 注释
    5. {{> box }} 载入子模块

加载 `JSON 数据`，调用 `JSContext` 将数据解析成 `Person 对象`的数组，并用 `Mustache 模板`呈现每个` Person`：
{% codeblock lang:swift %}
// 从文件`Persons.json`中加载json数据
if let peopleJSON = NSString(contentsOfFile:..., encoding: NSUTF8StringEncoding, error: nil) 
{
    // 获取js中定义的`loadPeopleFromJSON`的方法
    let load = context.objectForKeyedSubscript("loadPeopleFromJSON")
    // 通过调用load方法将`JSON 数据`解析成`Person 对象`的数组
    if let people = load.callWithArguments([peopleJSON]).toArray() as? [Person] 
    {
        // get rendering function and create template
        let mustacheRender = context.objectForKeyedSubscript("Mustache").objectForKeyedSubscript("render")
        let template = "{{getFullName}}, born {{birthYear}}"

        // loop through people and render Person object as string
        for person in people 
        {
            println(mustacheRender.callWithArguments([template, person]))
        }
    }
}

// Output:
// Grace Hopper, born 1906
// Ada Lovelace, born 1815
// Margaret Hamilton, born 1936
{% endcodeblock %}
JavaScript 代码段可能是附带应用一起发布的基本的用户定义的插件。
