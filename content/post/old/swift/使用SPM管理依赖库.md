---
title: 使用SPM管理依赖库
date: 2018-10-01 23:57:27
updated: 2018-10-03 16:55:41
comments: true
tags: [swift]
categories:
- 学习笔记
keywords: 
permalink: 
copyright: 
password: 
top:   
---
## 概念概述 
[Package Manager](https://swift.org/package-manager/#conceptual-overview)
{% github it-boyer PerfectTemplate 20294e56 width = 30% %}
### Modules模块
在 Swift 中我们使用模块来管理代码，每个模块指定一个命名空间并强制指定模块外哪些部分的代码是可以被访问控制的。

一个程序可以将它所有代码聚合在一个模块中，也可以将它作为依赖关系导入到其他模块。除了少量系统提供的模块，像 OS X 中的 Darwin 或者 Linux 中的 Glibc 等的大多数依赖需要代码被下载或者内置才能被使用。

当你将编写的解决特定问题的代码独立成一个模块时，这段代码可以在其他情况下被重新利用。例如，一个模块提供了发起网络请求的功能，在一个照片分享的 app 或者 一个天气的 app 里它都是可以使用的。使用模块可以让你的代码建立在其他开发者的代码之上，而不是你自己去重复实现相同的功能。

### Packages包
一个包由 Swift 源文件和一个清单文件组成。这个清单文件称为 `Package.swift`，定义包名或者它的内容使用`PackageDescription` 模块。
一个包有一个或者多个目标，每个目标指定一个产品并且可能声明一个或者多个依赖。
```sh
import PackageDescription
let package = Package(
    name: "DeckOfPlayingCards",
    products: [
        .library(name: "DeckOfPlayingCards", targets: ["DeckOfPlayingCards"]),
        .executable(name: "Dealer", targets: ["Dealer"]),
    ],
    dependencies: [
        .package(url: "https://github.com/apple/example-package-fisheryates.git", from: "2.0.0"),
        .package(url: "https://github.com/apple/example-package-playingcard.git", from: "3.0.0"),
    ],
    targets: [
        .target(
            name: "DeckOfPlayingCards",
            dependencies: ["FisherYates", "PlayingCard"]),
        .testTarget(
            name: "DeckOfPlayingCardsTests",
            dependencies: ["DeckOfPlayingCards"]),
    ]
)
```

### Products产品
一个`target`可能构建一个`.library()库`或者一个`.executable()可执行文件`作为其产品。
`.library(name: "", targets: [])`库:是包含用于其他Swift 代码导入该模块
`.executable(name: "", targets: [])`可执行文件:是一段可以被操作系统运行的程序。

### Dependencies依赖
`Dependencies`依赖是指`Package`中代码必须添加的`Modules`块。`Dependencies`由`.package`资源的`绝对路径`或`相对 URL` 和`包的版本`组成。
包管理器的作用是通过自动为工程下载和编译所有依赖的过程中，减少协调的成本。这是一个递归的过程：依赖能有自己的依赖，其中每一个也可以具有依赖，形成了一个依赖相关图。

## 用例

### 创建依赖库lib Package
1. 建一个`target`表示标准的52张扑克牌的`PlayingCard`：
```
mkdir PlayingCard
cd PlayingCard
swift package init
```
2. 创建公共实现类
`PlayingCard`定义`PlayingCard`类型，它由`Suit`枚举值(梅花、方块、红心、黑桃)和`Rank`枚举值(Ace、2、3、…、Jack、Queen、King)组成。
```
public enum Rank : Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King
}

public enum Suit: String {
    case Spades, Hearts, Diamonds, Clubs
}

public struct PlayingCard {
    let rank: Rank
    let suit: Suit
}
```
按照约定，一个`target`所有的源文件都存在相应的`Sources/<target-name>`目录下，默认情况下，`库模块`使用`public`来声明类型和方法，这些类型和方法位于`Sources/<target-name>`中：
```
example-package-playingcard
├── Sources
│   └── PlayingCard
│       ├── PlayingCard.swift
│       ├── Rank.swift
│       └── Suit.swift
└── Package.swift
```
`PlayingCard`因为不生成`executable`可执行文件，这种类型的`target`成为`库`，当构建一个模块可以被依赖导入使用。

3. 运行`swift build`命令，将编译生成`PlayingCard`模块。

### 使用 `#if #else #endif`构建配置语句
1. 创建`fisher`模块
```
mkdir fisher
cd fisher
swift package init
```
`fisher`与`PlayingCard`不同，此模块不定义任何新类型。相反，它扩展了现有的类型(特别是`CollectionType`和`MutableCollectionType`协议)，以添加`shuffle()`方法和它的变体副本`shuffleInPlace()`。
2. 使用构建配置语句 `#if #else #endif`
`shuffleInPlace()`的实现使用`Fisher-Yates`算法随机排列集合中的元素。因为Swift标准库不提供随机数生成器，所以该方法必须调用从系统模块导入的函数。为了使该函数与`macOS`和`Linux`兼容，代码使用构建配置语句。
在`macOS`中，系统模块是`Darwin`，它提供了`arc4random_uniform(_:)`函数。
在`Linux`中，系统模块为`Glibc`，它提供了`random()`函数:
```
#if os(Linux)
import Glibc
#else
import Darwin.C
#endif

public extension MutableCollectionType where Index == Int {
    mutating func shuffleInPlace() {
        if count <= 1 { return }

        for i in 0..<count - 1 {
            #if os(Linux)
                let j = Int(random() % (count - i)))) + i
            #else
                let j = Int(arc4random_uniform(UInt32(count - i))) + i
            #endif
                if i == j { continue }
                swap(&self[i], &self[j])
        }
    }
}
```
### 依赖库导入及使用
`DeckOfPlayingCards`包将前两个包组合在一起:它定义了一种桥牌类型，该类型在一个`PlayingCard`值数组上使用`fisher()`方法。
要使用`fisher`和`PlayingCards`模块，必须在`DeckOfPlayingCards`模块的清单文件`Package.swift`中声明为它们`package`依赖项。
```
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "DeckOfPlayingCards",
    products: [
        .library(name: "DeckOfPlayingCards", targets: ["DeckOfPlayingCards"]),
    ],
    dependencies: [
        .package(url: "https://github.com/apple/example-package-fisheryates.git", from: "2.0.0"),
        .package(url: "https://github.com/apple/example-package-playingcard.git", from: "3.0.0"),
    ],
    targets: [
        .target(
            name: "DeckOfPlayingCards",
            dependencies: ["FisherYates", "PlayingCard"]),
        .testTarget(
            name: "DeckOfPlayingCardsTests",
            dependencies: ["DeckOfPlayingCards"]),
    ]
)
```
每个依赖项指定源`URL`和`from:`版本号。
`源URL`是解析到Git存储库的当前用户可以访问的URL。版本需求遵循语义版本控制(SemVer)约定，用于确定签出哪个Git标签并使用它来构建依赖关系。对于依赖项`FisherYates` ，将使用最新版本主版本为2(例如，2.0.4)。类似地，`PlayingCard`依赖项将使用最新版本主版本为3。
当运行`swift build`命令时，包管理器下载所有依赖项，编译、并链接到包模块。这样`DeckOfPlayingCards`使用`import`语句来访问依赖模块中`public`类型的属性和方法。
您可以在项目根目录下的`.build/checkouts`目录中看到下载的源代码，在项目根目录下的`.build`目录中看到中间的构建产品。
## 解决依赖传递关系
构建`Dealer`模块,它依赖于`DeckOfPlayingCards`包，而`DeckOfPlayingCards`包又依赖于`PlayingCard`和`fisher`包。然而，由于Swift包管理器自动解析传递依赖项，您只需将`DeckOfPlayingCards`包声明为依赖项即可。
```
// swift-tools-version:4.0

import PackageDescription

let package = Package(
    name: "dealer",
    products: [
        .executable(name: "Dealer", targets: ["Dealer"]),
    ],
    dependencies: [
        .package(url: "https://github.com/apple/example-package-deckofplayingcards.git", from: "3.0.0"),
    ],
    targets: [
        .target(
        name: "Dealer",
        dependencies: ["DeckOfPlayingCards"]),
    ]
)
```
依赖传递关系也体现在Swift源文件代码`import`导入一个模块，就可以使用该某块下所有依赖库的类型。例如`Dealer`模块的`main.swift `文件。在`DeckOfPlayingCards`中的`Deck`类型和`PlayingCard`中的`PlayingCard`类型。尽管`Deck`类型的`shuffle()`方法在内部使用了`fisher`模块，但该模块不需要在`main.swift`中导入。
```
import PlayingCard
import DeckOfPlayingCards

let numberOfCards = 10

var deck = Deck.standard52CardDeck()
deck.shuffle()

for _ in 1...numberOfCards {
    guard let card = deck.deal() else {
        print("No More Cards!")
        break
    }
    print(card)
}
```
### 构建并运行可执行文件
根据约定，一个`target`的根目录中包含一个名为`main.swift`文件，可以构建成一个可执行文件。
运行`swift build`命令，然后运行`.build/debug`目录下的`Dealer`可执行文件。
```
$ swift build
$ ./.build/debug/Dealer
♠︎6
♢K
♢2
♡8
♠︎7
♣︎10
♣︎5
♢A
♡Q
♡7
```

### 两个坑
```
swift package generate-xcodeproj
```
1. 每次更新或添加库或框架，xcodeproj就需要重新创建一次，不然无法引用到新库或新框架；
2. 在source文件夹如果发生任何改动，库或框架的更新就会失败。
这就是为什么我要专门建一个工程来管理这些框架或者库（这是我踩的最恶心的坑）。为了解决这个大坑，就再创建一个项目工程，然后使用workspace来管理
[今天开始用swift写服务器(一)](https://blog.csdn.net/loveqcx123/article/details/70160379)
