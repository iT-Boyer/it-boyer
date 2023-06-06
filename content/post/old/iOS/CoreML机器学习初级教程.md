---
title: CoreML机器学习初级教程
date: 2017-06-24 22:10:44
updated: 2017-06-24 22:24:54
comments: true
tags: [CoreML]
categories:
- 学习笔记
keywords: 
permalink: 
---
### 资源
[Core ML开发文档](https://developer.apple.com/documentation/coreml)
[ML模型资源页面](https://developer.apple.com/machine-learning/)
在Working with Models中包含有几个常用的模型模板，例如用于在图片中检测物体——树、动物、人等等。
[Integrating a Core ML Model into Your App](https://developer.apple.com/documentation/coreml/integrating_a_core_ml_model_into_your_app)


[官方Core ML文档示例 App](https://docs-assets.developer.apple.com/published/51ff0c1668/IntegratingaCoreMLModelintoYourApp.zip)
MarsHabitatPricePredictor 模型的输入只是数字，因此代码直接使用生成的 MarsHabitatPricer 方法和属性，而不是将模型包装在 Vision 模型中。每次都改一下参数，很容易看出模型只是一个线性回归：
137 * solarPanels + 653.50 * greenHouses + 5854 * acres

### 配置 ：将 Core ML 模型集成到你的 App
本教程使用 Places205-GoogLeNet 模型，可以从苹果的[ML](https://developer.apple.com/machine-learning/)页面下载。往下滑找到 Working with Models，下载第一个。还在这个页面，注意一下其它三个模型，它们都用于在图片中检测物体——树、动物、人等等。
>注意：如果你有一个训练过的模型，并且是使用受支持的机器学习工具训练的，例如 Caffe、Keras 或 scikit-learn，Converting Trained Models to Core ML 介绍了如何将其转换为 Core ML 格式。

1. 添加模型
下载 GoogLeNetPlaces.mlmodel 后，把它从 Finder 拖到项目导航器的 Resources 组里：
![](http://upload-images.jianshu.io/upload_images/861914-5425960c41207b82.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 生成模型类
选择该文件，然后等一会儿。Xcode 生成了模型类后会显示一个箭头：
![](http://upload-images.jianshu.io/upload_images/861914-e00a802f64f0ae13.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 查看模型类
点击箭头，查看生成的类：
![](http://upload-images.jianshu.io/upload_images/861914-1268b5b918fccf07.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
三个类：
`GoogLeNetPlaces`: 主类，包含一个 model 属性和两个 prediction 方法
`GoogLeNetPlacesInput`: 输入类,包含一个 CVPixelBuffer 类型的 sceneImage 属性，Vision 框架会负责把我们熟悉的图片格式转换成正确的输入类型。
`GoogLeNetPlacesOutput`：输出属性,Vision 框架会将 `GoogLeNetPlacesOutput` 属性转换为自己的 `results` 类型.

### 实现Vision工作流程
标准的 Vision 工作流程是创建模型，创建一或多个请求，然后创建并运行请求处理程序。
并管理对 `prediction `方法的调用，所以在所有生成的代码中，我们只会使用 `model` 属性。

1. 创建模型：在 `Vision Model` 中包装 `Core ML Model`
CoreML模型 是用于 Vision 请求的 Core ML 模型的容器
打开 ViewController.swift，并在 import UIKit 下面 import 两个框架：
```swift
import CoreML
import Vision
```
2. 创建`VNCoreMLRequest`图像分析请求
`VNCoreMLRequest` 是一个图像分析请求，它使用 Core ML 模型来完成工作。它的 completion handler 接收 request 和 error 对象。
Core ML 模型`GoogLeNetPlaces` 是一个分类器，因为它仅预测一个特征：图像的场景分类。这时request.results 是 `VNClassificationObservation` 对象数组。
```
// 创建一个带有 completion handler 的 Vision 请求
let request = VNCoreMLRequest(model: model) { [weak self] request, error in
    guard let results = request.results as? [VNClassificationObservation],
    let topResult = results.first else {
    fatalError("unexpected result type from VNCoreMLRequest")
}

// 在主线程上更新 UI
let article = (self?.vowels.contains(topResult.identifier.first!))! ? "an" : "a"
    DispatchQueue.main.async { [weak self] in
        self?.answerLabel.text = "\(Int(topResult.confidence * 100))% it's \(article) \(topResult.identifier)"
    }
}
```
VNClassificationObservation 有两个属性：identifier - 一个 String，以及 confidence - 介于0和1之间的数字，这个数字是是分类正确的概率。使用对象检测模型时，你可能只会看到那些 confidence 大于某个阈值的对象，例如 30％ 的阈值。
然后取第一个结果，它会具有最高的 confidence 值，然后根据 identifier 的首字母把不定冠词设置为“a”或“an”。最后，dispatch 回到主线程来更新 label。你很快会明白分类工作为什么不在主线程，因为它会很慢。

### 创建并运行VNImageRequestHandler请求处理程序
`VNImageRequestHandler` 是标准的 Vision 框架请求处理程序；不特定于 Core ML 模型。给它 image 作为 detectScene(image:) 的参数。然后调用它的 perform 方法来运行处理程序，传入请求数组。在这个例子里，我们只有一个请求。
把下面几行添加到 `detectScene(image:)` 的末尾：
```swift
// 在主线程上运行 Core ML GoogLeNetPlaces 分类器
let handler = VNImageRequestHandler(ciImage: image)
DispatchQueue.global(qos: .userInteractive).async {
    do {
        try handler.perform([request])
    } catch {
        print(error)
    }
}
```

### 使用模型来自动识别场景
在两个地方调用 `detectScene(image:) `
把下面几行添加到 viewDidLoad() 的末端和 imagePickerController(_:didFinishPickingMediaWithInfo:) 的末端：
```
guard let ciImage = CIImage(image: image) else {
    fatalError("couldn't convert UIImage to CIImage")
}

detectScene(image: ciImage)
```
现在构建并运行。
1. 场景一:
机器识别出了50%的概率是摩天大厦
![](http://upload-images.jianshu.io/upload_images/861914-bb5bec38118f91ff.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 场景二
机器人识别出了75%的概率是水族池
![](http://upload-images.jianshu.io/upload_images/861914-e194482f995324a1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 什么是深度学习
自20世纪50年代以来，AI 研究人员开发了许多机器学习方法。苹果的 Core ML 框架支持神经网络、树组合、支持向量机、广义线性模型、特征工程和流水线模型。但是，神经网络最近已经取得了很多极为神奇的成功，开始于 2012 年谷歌使用 YouTube 视频训练 AI 来识别猫和人。仅仅五年后，谷歌正在赞助一场确定 5000 种植物和动物的比赛。像 Siri 和 Alexa 这样的 App 也存在它们自己的神经网络。
神经网络尝试用节点层来模拟人脑流程，并将节点层用不同的方式连接在一起。每增加一层都需要增加大量计算能力：Inception v3，一个对象识别模型，有48层以及大约2000万个参数。但计算基本上都是矩阵乘法，GPU 来处理会非常有效。GPU 成本的下降使我们能够创建多层深度神经网络，此为深度学习。
神经网络，circa 2016
神经网络需要大量的训练数据，这些训练数据理想化地代表了全部可能性。用户生成的数据爆炸性地产生也促成了机器学习的复兴。
训练模型意味着给神经网络提供训练数据，并让它计算公式，此公式组合输入参数以产生输出。训练是离线的，通常在具有多个 GPU 的机器上。
要使用这个模型，就给它新的输入，它就会计算输出：这叫做推论。推论仍然需要大量计算，以从新的输入计算输出。因为有了 Metal 这样的框架，现在可以在手持设备上进行这些计算。
在本教程的结尾你会发现，深度学习远非完美。真的很难建立具有代表性的训练数据，很容易就会过度训练模型，以至于它会过度重视一些古怪的特征。
苹果提供了什么？
苹果在 iOS 5 里引入了 NSLinguisticTagger 来分析自然语言。iOS 8 出了 Metal，提供了对设备 GPU 的底层访问。
去年，苹果在 Accelerate 框架添加了 Basic Neural Network Subroutines (BNNS)，使开发者可以构建用于推理（不是训练）的神经网络。
今年，苹果给了我们 Core ML 和 Vision！
Core ML 让我们更容易在 App 中使用训练过的模型。
Vision 让我们轻松访问苹果的模型，用于面部检测、面部特征点、文字、矩形、条形码和物体。
你还可以在 Vision 模型中包装任意的图像分析 Core ML 模型，我们在这篇教程中就干这个。由于这两个框架是基于 Metal 构建的，它们能在设备上高效运行，所以不需要把用户的数据发送到服务器。


