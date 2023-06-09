## 1、什么是 GenICam

GenICam 是一个全球标准协议，**定义了用于计算机控制数码相机和其他传输视频的成像产品的通用接口**。

- GenICam 允许业界使用相同的接口为任何兼容的相机或成像传输产品编程应用程序，而不管其供应商、实施细节、功能集或接口技术如何。

- GenICam 接口已被纳入一系列高速视频标准，包括 GigE Vision、USB3 Vision 和 Camera Link，为之提供了一个端到端配置接口。

<img src="file:///E:/MarkText/image cache/2022-09-17-10-32-48-image.png" title="" alt="" data-align="center">

## 2、优势

通过以下方式降低高速视频应用程序的设计、部署和维护成本：

1. 允许将相同的应用程序设计框架用于许多不同类型的相机和成像产品；

2. 共享使用为相机和系统制造商提供了一组通用的功能名称和配置，简化将相机或其他成像产品集成到系统中的过程。

## 3、相关

GigE Vision 和 USB3 Vision 标准规定使用 GenICam 接口来访问和控制兼容相机或其他成像产品饿功能。

## 4、标准

GenICam 标准是由欧洲机器视觉协会 (EMVA) 开发的。

GenICam 标准由 4 个模块组成：

1. GenApi —— 用于设置相机的应用程序开发接口，配置摄像头。定义可扩展标记语言（XML）文件的格式，该文件捕获并指定如何访问和控制兼容相机或其他成像产品的功能。GenICam 兼容设备必须包含符合此格式的 XML 文件；

2. 标准功能命名约定（SNFC）—— 标准化 220 多种常用相机功能的名称和型号，包括像素格式命名约定（PFNC）；

3. FWUpdate —— 使用 GenApi 更新设备；

4. CLProtoco I —— 允许通过 GenApi 访问符合 Camera Link 标准的相机；

5. GenCP —— 通用控制协议；

6. GenTL —— 定义了一些传统图像采集卡制造商提供的软件接口（传输层接口），获取图像用的传输层（TL）协议；

7. GenTL SNFC —— 传输层接口的推荐名称和类型。

## 5、工作原理

GenICam 标准的核心是一个在 XML 描述符文件里对相机属性进行的描述。使用此文件时，从 XML 到 C++ 的翻译程序会直接生成被称为 GenAPI 的应用编程接口或图形用户界面（GUI）的元素。

<img src="file:///E:/MarkText/image cache/2022-09-17-11-27-14-image.png" title="" alt="" data-align="center">

# 
