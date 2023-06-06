---
title: hackmd使用
date: 2016-12-21 10:49:32
updated: 2017-09-23 17:28:44
tags: [hexo]
categories:
- 博客站务
---


編輯
===
## 快速鍵 
跟又快又方便的Sublime text很像
> 更多訊息請至 [這裡](https://codemirror.net/demo/sublime.html)

## 自動完成
提供完整的 Markdown 自動完成與提示
- 表情符號：輸入 `:` 顯示提示
- 程式碼區塊：輸入 ` 3个` ` 加上一個字元 顯示提示 
- 標頭：輸入 `#` 顯示提示
- 參考：輸入 `[]` 顯示提示
- 外部：輸入 `{}` 顯示提示
- 圖片：輸入 `!` 顯示提示

## 標題
會使用 **第一個第一級標頭** 作為筆記標題

## 標籤
如同以下方式來使用標籤，它們會顯示在您的 **歷史紀錄**
###### tags: `功能` `酷` `更新`

## [YAML metadata](./yaml-metadata)
提供描述筆記的資訊，以進階設定瀏覽行為，詳細請至上連結
- robots: 設定網路機器人 meta
- lang: 設定瀏覽器顯示語言
- dir: 設定文字方向
- breaks: 設定是否使用分行
- mathjax: 設定是否使用 mathjax

## 表情符號
您可以像是這樣使用表情符號 :smile: :smiley: :cry: :wink:
> 完整的表情符號列表 [在這裡](http://www.emoji-cheat-sheet.com/)

## 待辦清單
- [ ] 待辦
    - [x] 買些沙拉
    - [x] 刷牙
    - [ ] 喝水
<!--more-->
## 程式碼區塊
我們支援非常多程式語言，使用自動完成來看看有些什麼
```javascript=
var s = "JavaScript syntax highlighting";
alert(s);
function $initHighlight(block, cls) {
  try {
    if (cls.search(/\bno\-highlight\b/) != -1)
      return process(block, true, 0x0F) + 
             ' class=""';
  } catch (e) {
    /* handle exception */
  }
  for (var i = 0 / 2; i < classes.length; i++) {
    if (checkCondition(classes[i]) === undefined)
      return /\d+[\s/]/g;
  }
}
```
> 如果想要 **行號**，在表明程式語言之後輸入 `=`
> 您也可以指定開始行號，如下所示，行號從101開始

```javascript=101
var s = "JavaScript syntax highlighting";
alert(s);
function $initHighlight(block, cls) {
  try {
    if (cls.search(/\bno\-highlight\b/) != -1)
      return process(block, true, 0x0F) + 
             ' class=""';
  } catch (e) {
    /* handle exception */
  }
  for (var i = 0 / 2; i < classes.length; i++) {
    if (checkCondition(classes[i]) === undefined)
      return /\d+[\s/]/g;
  }
}
```

> 或是可以接續上一個程式碼區塊的行號，使用 `=+`

```javascript=+
var s = "JavaScript syntax highlighting";
alert(s);
```

## 引用區塊標籤
> 您可以使用以下語法，表明自己的 **姓名、時間與顏色** 並與其他的引用區塊做區別
> [name=ChengHan Wu] [time=Sun, Jun 28, 2015 9:59 PM] [color=#907bf7]
> > 也支援巢狀引用區塊喔！
> > [name=ChengHan Wu] [time=Sun, Jun 28, 2015 10:00 PM] [color=red]

## 外部

### Youtube
{%youtube 1G4isv_Fylg %}

### Vimeo
{%vimeo 124148255 %}

### Gist
{%gist schacon/4277%}

### SlideShare
```js
{%slideshare briansolis/26-disruptive-technology-trends-2016-2018-56796196 %}
```
### Speakerdeck
```js
{%speakerdeck sugarenia/xxlcss-how-to-scale-css-and-keep-your-sanity %}
```
### PDF
**注意：請使用 https 的網址，否則可能會被您的瀏覽器阻擋載入**
```js
{%pdf https://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf %}
```

## MathJax

您可以使用 **MathJax** 語法 來產生 *LaTeX* 數學表達式，如同 [math.stackexchange.com](http://math.stackexchange.com/)：

The *Gamma function* satisfying $\Gamma(n) = (n-1)!\quad\forall n\in\mathbb N$ is via the Euler integral

$$
x = {-b \pm \sqrt{b^2-4ac} \over 2a}.
$$

$$
\Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,.
$$

> 更多關於 **LaTeX** 數學表達式 [請至這裡](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference)

## UML 圖表

### 循序圖

您可以像是以下使用循序圖：

```sequence
艾莉絲->包柏: 哈摟，你好嗎？
Note right of 包柏: 包柏思考中
包柏-->艾莉絲: 我很好，謝謝！
Note left of 艾莉絲: 艾莉絲回應
艾莉絲->包柏: 最近過得怎樣？
```

### 流程圖

您可以像是以下使用流程圖：
```flow
st=>start: 開始
e=>end: 結束
op=>operation: 我的操作
op2=>operation: 啦啦啦
cond=>condition: 是或否？

st->op->op2->cond
cond(yes)->e
cond(no)->op2
```

### Graphviz
```graphviz
digraph hierarchy {

                nodesep=1.0 // increases the separation between nodes
                
                node [color=Red,fontname=Courier,shape=box] //All nodes will this shape and colour
                edge [color=Blue, style=dashed] //All the lines look like this

                Headteacher->{Deputy1 Deputy2 BusinessManager}
                Deputy1->{Teacher1 Teacher2}
                BusinessManager->ITManager
                {rank=same;ITManager Teacher1 Teacher2}  // Put them on the same level
}
```

### Mermaid
```mermaid
gantt
    title A Gantt Diagram

    section Section
    A task           :a1, 2014-01-01, 30d
    Another task     :after a1  , 20d
    section Another
    Task in sec      :2014-01-12  , 12d
    anther task      : 24d
```

> 更多關於 **循序圖** 語法 [在這裡](http://bramp.github.io/js-sequence-diagrams/).
> 更多關於 **流程圖** 語法 [在這裡](http://adrai.github.io/flowchart.js/).
> 更多關於 **Graphviz** 語法 [在這裡](http://www.tonyballantyne.com/graphs.html)
> 更多關於 **Mermaid** 語法 [在這裡](http://knsv.github.io/mermaid)

警告區塊
---
:::success
耶 :tada:
:::

:::info
這是訊息 :mega:
:::

:::warning
注意 :zap:
:::

:::danger
喔不 :fire:
:::


## 排版

### 標頭

```
# h1 標頭
## h2 標頭
### h3 標頭
#### h4 標頭
##### h5 標頭
###### h6 標頭
```

### 水平分隔線

___

---

***


### 字形替換

(c) (C) (r) (R) (tm) (TM) (p) (P) +-

測試.. 測試... 測試..... 測試?..... 測試!....

!!!!!! ???? ,,

Remarkable -- awesome

"Smartypants, 雙引號"

'Smartypants, 單引號'

### 強調

**這是粗體文字**

__這是粗體文字__

*這是斜體文字*

_這是斜體文字_

~~這是刪除文字~~

上標： 19^th^

下標： H~2~O

++這是底線文字++

==這是標記文字==


### 引用區塊


> 引用區塊也可以是巢狀的喔...
>> ...可以多層次的使用...
> > > ...或是用空白隔開 


### 清單

#### 項目

+ 在行開頭使用 `+` `-` 或是 `*` 來建立清單
+ 空兩個空白就可以產生子清單
  - 當清單標記使用的字元不同，會強制建立新的清單
    * Ac tristique libero volutpat at
    + Facilisis in pretium nisl aliquet
    - Nulla volutpat aliquam velit
+ 非常簡單！

#### 編號

1. Lorem ipsum dolor sit amet
2. Consectetur adipiscing elit
3. Integer molestie lorem at massa


1. 您可以逐次增加項目數字...
1. ...或是全部都使用 `1.`
1. feafw
2. 332
3. 242
4. 2552
1. e2

從其他範圍開始編號清單

57. foo
1. bar

### 程式碼

行內 `程式碼`

縮排程式碼

    // Some comments
    line 1 of code
    line 2 of code
    line 3 of code


程式碼區塊

```
Sample text here...
```

語法標色

``` javascript
var foo = function (bar) {
  return bar++;
};

console.log(foo(5));
```

### 表格

| 選項 | 描述 |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

向右對齊

| 選項 | 描述 |
| ------:| -----------:|
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

向左對齊

| 選項 | 描述 |
|:------ |:----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |

置中對齊

| 選項 | 描述 |
|:------:|:-----------:|
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |


### 連結
[連結文字](http://dev.nodeca.com)
[加上標題的連結文字](http://nodeca.github.io/pica/demo/ "標題文字！")
自動轉換連結 https://github.com/nodeca/pica


### 圖片
![Minion](https://octodex.github.com/images/minion.png)
![Stormtroopocat](https://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")
如同連結一般，圖片也可以用註腳語法
![Alt text][id]
使用參考，可以在稍後的文件中再定義圖片網址

[id]: https://octodex.github.com/images/dojocat.jpg  "The Dojocat"

![Minion](https://octodex.github.com/images/minion.png =200x200)
使用指定的大小顯示圖片

### 註腳

註腳 1 連結[^first].
註腳 2 連結[^second].
行內註腳^[行內註腳的文字] 定義
重複的註腳參考[^second].

[^first]: 註腳 **也可以標記**
    還可以有很多段落
[^second]: 註腳 文字

### 定義清單

名詞 1

:   定義 1 快速連續項目

名詞 2 加上 *行內標記*

:   定義 2

        { 這些程式碼屬於 定義 2 的一部分 }

    定義 2 的第三段落

_緊密樣式：_

名詞 1
  ~ 定義 1

名詞 2
  ~ 定義 2a
  ~ 定義 2b

### 縮寫

這是 HTML 的縮寫範例
它會轉換 "HTML"，但是縮寫旁邊其他的部分，例如："xxxHTMLyyy"，不受影響

*[HTML]: Hyper Text Markup Language
