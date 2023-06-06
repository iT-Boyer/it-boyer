---
title: Hexo标签插件
date: 2017-01-23 15:17:33
tags: [hexo]
categories:
- 博客站务
---


标签插件和 Front-matter 中的标签不同，它们是用于在文章中快速插入特定内容的插件。

## 引用块
---
在文章中插入引言，可包含作者、来源和标题。

### 别号： quote
---
```js
{% blockquote [author[, source]] [link] [source_link_title] %}
content
{% endblockquote %}
```
#### 样例
---   
##### 无参普通blockquote
---
```js
{% blockquote %}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque hendrerit lacus ut purus iaculis feugiat. Sed nec tempor elit, quis aliquam neque. Curabitur sed diam eget dolor fermentum semper at eu lorem.
{% endblockquote %}
```
{% blockquote %}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque hendrerit lacus ut purus iaculis feugiat. Sed nec tempor elit, quis aliquam neque. Curabitur sed diam eget dolor fermentum semper at eu lorem.
{% endblockquote %}
<!--more-->
##### 引用书上的句子
---
```js
{% blockquote David Levithan, Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
{% endblockquote %}
```
{% blockquote boyer huo , Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
{% endblockquote %}

##### 引用 Twitter
---
```js
{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}
```
{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}
##### 引用网络上的文章
---
```js
{% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endblockquote %}
```
{% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endblockquote %}

## 代码块
---
在文章中插入代码。

### 别名:code
---
```js
{% codeblock [title] [lang:language] [url] [link text] %}
code snippet
{% endcodeblock %}
```
#### 样例
---
##### 普通的代码块
---
```js
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}
```
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}
##### 指定语言
---
```js
{% codeblock lang:objc %}
[rectangle setX: 10 y: 10 width: 20 height: 20];
{% endcodeblock %}
```
{% codeblock lang:objc %}
[rectangle setX: 10 y: 10 width: 20 height: 20];
{% endcodeblock %}
##### 附加说明
---
```js
{% codeblock Array.map %}
array.map(callback[, thisArg])
{% endcodeblock %}
```
{% codeblock Array.map %}
Array.map
array.map(callback[, thisArg])
{% endcodeblock %}
##### 附加说明和网址
---
```js
{% codeblock _.compact http://underscorejs.org/#compact Underscore.js %}
_.compact([0, 1, false, 2, '', 3]);
=> [1, 2, 3]
{% endcodeblock %}
```
{% codeblock _.compact http://underscorejs.org/#compact Underscore.js %}
_.compactUnderscore.js
_.compact([0, 1, false, 2, '', 3]);
=> [1, 2, 3]
{% endcodeblock %}

## 反引号代码块 (MD语法)
---
### 样例
---
#### 行内代码块
---
```md
行内 `code 块 `
```
#### 缩进代码块
---
```md
    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code
```
#### 多行代码块
---
```md 
``` [language] [title] [url] [link text] 

    代码块

 `` `   

```
## 表格
---
### 样例
---
#### 默认左对齐
---
```md
|参数|描述|默认值|
|-------|-------|----------|
|文本内容 |文本内容| 文本内容  |
```

|参数|描述|默认值|
|-------|-------|----------|
|layout | 布局	 |          |
|title  | 标题   |	    |
|date   |建立日期 |文件建立日期|
|updated|更新日期 |文件更新日期|
|comments|开启文章的评论功能|true|
|tags	|标签（不适用于分页）|	|
|categories|分类（不适用于分页）|	|
|permalink|覆盖文章网址|     |
#### 向右对齐
---
```md
|参数|描述|默认值|
|-------:|-------:|-------:|
| 文本内容 | 文本内容 | 文本内容|
```
|三种布局|路径:(储存到路径文件夹)|
|------:|------------:|
|post|	source/_posts|
|page|	source|
|draft|	source/_drafts|
#### 向左对齐
---
```md
|参数|描述|默认值|
|:------|:------|:------|
| 文本内容 | 文本内容 | 文本内容|
```
|日期变量	| 描述:（可以通过日期来管理文章） |
|:------|:---------------------------|
|:title	|标题（小写，空格将会被替换为短杠）|
|:year	|建立的年份，比如， 2015|
|:month	|建立的月份（有前导零），比如， 04|
|:i_month|	建立的月份（无前导零），比如， 4|
|:day	|建立的日期（有前导零），比如， 07|
|:i_day	|建立的日期（无前导零），比如， 7|

#### 居中对齐
---
```md
|参数|描述|默认值|
|:------:|:------:|:------:|
| 文本内容 | 文本内容 | 文本内容|
```
|变量 | 描述|
|:------:|:------:|
|layout | 布局当:false不加任何布局样式|
|title | 标题|
|date | 文件建立日期|
## Pull Quote
---
在文章中插入 Pull quote。
```js
{% pullquote [class] %}
content
{% endpullquote %}
```
## jsFiddle
---
[官网](https://jsfiddle.net)
在文章中嵌入 `jsFiddle` 在线的shell编辑器,可以供我们在线测试html、js、和css代码。
```js
{% jsfiddle shorttag [tabs] [skin] [width] [height] %}
```
## Gist
---
在文章中嵌入 Gist
```js
{% gist gist_id [filename] %}
```
`filename`: 可选，当不指定文件名时，嵌入显示`gist_id`下所有文件。

一个gist可能存在多个文件：
``` 
https://gist.github.com/dergachev/4627207#file-gif-screencast-osx-md
```
`4627207`：表示gist_id ，`#file-`后边内容：表示Gist中某个文件名

如下:指定文章中嵌入`ecba275d5e4404678354`中的`NSAttributeString相关方法.m`内容。
```
{% gist ecba275d5e4404678354 NSAttributeString相关方法.m %}
```

## iframe
---
在文章中插入 iframe。
```js
{% iframe url [width] [height] %}
```
## 插入图片
---
### 样例
---
#### Hexo语法
---
在文章中插入指定大小的图片。
```js
{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
```
#### MD语法
---
##### 原图+toolTip
---
```
![boyer logo](http://boyers.coding.me/img/logo.png "这是我的logo图片")
```
![boyer logo](http://boyers.coding.me/img/logo.png "这是我的logo图片")

#####  注脚语法
---
可以在稍後的文件中再定义图片地址
```
![boyer logo][logo]
[logo]: http://boyers.coding.me/img/logo.png  "这是我的logo图片"
```
![boyer logo][logo]
[logo]: http://boyers.coding.me/img/logo.png  "这是我的logo图片"

##### 指定图片大小
```
![boyer logo](http://boyers.coding.me/img/logo.png [200] [200] "这是我的logo图片")
```
![boyer logo](http://boyers.coding.me/img/logo.png [200] [200] "这是我的logo图片")

## 超链接
---
### 样例
---
#### Hexo语法
---
在文章中插入链接，并自动给外部链接添加 target="_blank" 属性。
```js
{% link text url [external] [title] %}
```
#### MD语法
---
##### 智能识别超链接
---
```
http://boyers.coding.me
```
http://boyers.coding.me
##### 文本式
---
```md
[boyer Blog](http://boyers.coding.me)
```
[boyer Blog](http://boyers.coding.me)
##### toolTip式
---
```md
[boyer Blog](http://boyers.coding.me "这是我的博客首页")
```
[boyer Blog](http://boyers.coding.me "这是我的博客首页")
## Include Code
---
插入 source 文件夹内的代码文件(.m/json/xml等)。
```js
{% include_code [title] [lang:language] path/to/file %}
```
## Youtube
---
在文章中插入 Youtube 视频。
```js
{% youtube video_id %}
```
## Vimeo
---
在文章中插入 Vimeo 视频。
```js
{% vimeo video_id %}
```
## 引用文章
---
根据服务器根目录分为两种方式：
```js
{% post_path slug %}
{% post_link slug [title] %}
```
以`Docker使用`博客为例
1. 相对路径
    ```js
    {% post_path Docker使用 %}
    ```
    相对于服务器根目录的位置：
    {% post_path Docker使用 %}
2. 绝对路径
    ```js
    {% post_link Docker使用 Docker使用 %}
    ```
    本博中其他文章的超链接：
    {% post_link Docker使用 Docker使用 %}

## 引用资源
---
引用文章的资源。
Assets指的是那些不在source目录下的资源，比如图片、CSS文件或者Javascript文件。Hexo提供一种更方便的方法来管理这些资源（Assets）。想使其生效，首先修改 post_asset_folder 字段的设置，将其值改为 true 。
当生效后，在你创建文章的时候，Hexo会创建一个同名目录，你可以将该文章关联的资源全部放到该目录下。这样就可以更加方便的使用它们了。
使用方法就是上面介绍过的标签插件。
```md
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```
## Raw
---
一些内容不想被主题渲染，可以使用该插件呈现原始状态。
如果您想在文章中插入 Swig 标签，可以尝试使用 Raw 标签，以免发生解析异常。
```js
{% raw %}
content
{% endraw %}
```

