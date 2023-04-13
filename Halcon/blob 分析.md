# Blob 分析

- Blob，即 binary large object，二进制大型对象，是事务处理过程中遇到的一些大型的、复杂的数据项，必须作为一个完整的数据项看待。如一幅图形、一帧图像、一段语言等；

- 计算机视觉中的 Blob 是指：图像中的具有相似颜色、纹理等特征所组成的一块连通区域；

- Blob 分析是对图像中相同像素的连通域进行分析(该连通域称为Blob)；

- 过程：将图像进行二值化，分割得到前景和背景，然后进行连通域检测，从而得到 Blob 块。

> 1. Blob 分析不是对单个像素逐一分析，而是对图像的行进行操作；图像的每一行都用游程长度编码（RLE）来表示相邻的目标范围；
> 
> 2. 常用于二维图像、高对比度图像、存在/缺陷检测、数值范围和旋转不变性需求；
> 
> 3. **瑕疵：灰度突变**。

# 示例

# 算子

## open_framegrabber

- `打开并配置图像采集设备`

```cpp
open_framegrabber ('GigEVision2', 0, 0, 0, 0, 0, 0, 'progressive', -1, \ 
                   'default', -1, 'false', 'default','default', 0, -1, \
                   AcqHandle)
```

1. Name : **图像采集接口名称**，即对应的 DLL 或共享库(Linux/macOS)的名称；

2. HorizontalResolution : 水平分辨率（默认绝对值或全分辨率为 1，四分之一分辨率为 4）；

3. VerticalResolution : 垂直分辨率；

4. ImageWidth ：宽度(HorizontalResolution - 2*StartColumn 的绝对值或 0)；

5. ImageHeight ：高度(VerticalResolution - 2*StartRow 的绝对值或 0)；

6. StartRow ：图像左上角的行号（如果ImageHeight=0，则为边框高度）；

7. StartColumn ：图像左上角的列号（如果ImageWidth=0，则为边框宽度）；

8. Field ：所需半或全图像（'first', 'second', 'next', 'interlaced', 'progressive', 'default'）；

9. BitsPerChannel ：每像素和图像通道传输的位数（默认 -1）；

10. ColorSpace ：图像颜色格式，单通道图像"gray"或"raw"或三通道图像的"rgb"或"yuv"；

11. Generic ：具有设备特定含义的泛型参数（默认 -1）；

12. ExternalTrigger ：外部触发器；

13. CameraType ：相机类型（ 'ntsc', 'pal', 'auto', 'default' ）；

14. Device ：图像采集设备所连接的设备；

15. Port ：图像采集设备连接到的端口（默认 -1）；

16. LineIn ：多路复用器的摄像头输入线（默认 -1）；

17. AcqHandle ：打开的图像采集设备的句柄。

## grab_image_start

- `从指定的图像采集设备启动异步抓取`

```cpp
grab_image_start (AcqHandle, -1)
```

1. 使用操作符 open_framegrabber 和 set_framegrabber_param 指定**图像采集**设备所需的操作模式、合适的图像部分和附加的特定于接口的设置；

2. 与 **grab_image_async 或 grab_data_async** 一起使用时才有意义；

3. 可以使用 **set_framegrabber_param** 操作符和参数 'do_abort_grab' 来中止抓取；

4. MaxDelay ：**已弃用**。

## grab_image_async

- `从指定的图像采集设备异步抓取图像`

```cpp
grab_image_async (Image, AcqHandle, -1)
```

1. 自异步抓取开始以来如果已经超过 MaxDelay ms，则认为异步抓取的映像太旧，并在必要时抓取一个新映像。如果 MaxDelay 被赋值为负值，则该控制机制将被禁用；

2. 如果在 grab_image_async 之后调用操作符 grab_image 或 grab_data，则启动的异步抓取将被中止，并**启动一个新的同步抓取**；

3. MaxDelay ：异步抓取开始和图像交付之间的最大容许延迟【ms】。

## rgb1_to_gray

- `将 RGB 图像转换为灰度图像`

```cpp
rgb1_to_gray (Image, GrayImage)
```

1. RGB 图像的三个通道作为输入图像的前三个通道传递；

2. 图像按照以下公式进行变换：灰度=0.299 \* 红色 + 0.587 \* 绿色 + 0.114 \* 蓝色；

3. 如果 RGB 图像中的一幅输入图像是单通道图像，它的引用将被简单地复制到输出灰度图像。

## regiongrowing

- `使用区域增长分割图像`

```cpp
regiongrowing (Image, Regions, 3, 3, 6, 100)
```

1. 将图像分割成相同强度的区域-光栅成大小为 RasterHeight RasterWidth 的矩形；

2. 为了判断相邻的两个矩形是否属于同一区域，只使用它们中心点的灰度值。如果灰度值差小于或等于容差，则矩形合并为一个区域；

3. 所产生的区域是所选大小的矩形的集合。只返回包含至少MinSize点的区域；

4. RasterHeight、RasterWidth  ：测试像素之间的垂直、水平距离（光栅的高度，默认 3）；

5. Tolerance ：灰度值差小于或等于公差的点累积到同一对象中，默认 6；

6. MinSize ：输出区域的最小尺寸。

## 对象处理

```cpp
count_obj(Regions, Number) // 元组中的对象数
select_obj(Regions, Selected, Index) // 从对象元组中选择对象
```

## 文件处理

```cpp
open_file('C://log.txt','output',FileHandle) // 打开文本或二进制格式的文件
fwrite_string(FileHandle,'将字符串和数字写入文本文件')
fnew_line(FileHandle) // 写一个换行符并清除输出缓冲区
close_file(FileHandle)
```

## 开运算

- **先腐蚀后膨胀**；

- `opening_circle` ：使用圆形结构元素**打开**区域。

```cpp
opening_circle(ConnectedRegions, RegionOpening, 3.5)
```

1. opening_circle 被定义为一个**侵蚀**，后面跟着一个带有圆形结构元素的 Minkowski 加法；

2. 开放的作用是消除小区域(小于圆形结构元素)，并平滑区域的边界；

3. Radius : 圆形结构元素的半径（默认 3.5）。

## 闭运算

- **先膨胀后腐蚀**；

- `closing_circle` ：使用圆形结构元素**封闭**区域。

```cpp
closing_circle(Region : RegionClosing : Radius : )
```

1. 类似于 close，即区域的边界被平滑，区域内小于半径的圆形结构元素的孔被关闭；

2. closing_circle 被定义为一个**膨胀**，然后是 Minkowski 减法，两者都有相同的圆形结构元素；

3. Radius : 圆形结构元素的半径（默认 3.5）。

## 腐蚀

- `erosion_circle` ：使用圆形结构元素**侵蚀区域**。

```cpp
erosion_circle(Region, RegionErosion, 3.5)
```

1. 对输入区域 Region 应用带有圆形结构元素的闵可夫斯基 Minkowski **减法**；

2. **减少区域，平滑区域边界，并消除小于圆形掩码的区域**；

3. 只选择像 3.5（默认）、5.5 这样的值是有用的，因为整数半径会导致一个圆的重心不是整数，它会四舍五入到下一个整数。

## 膨胀

- `dilation_circle` ：使用圆形结构元素**扩张区域**。

```cpp
dilation_circle(Region, RegionDilation, 3.5)
```

1. 对输入区域 Region 应用带有圆形结构元素的 Minkowski **加法**；

2. **区域扩大，区域边界平滑，区域内部小于圆形掩模的孔被关闭**；

3. 选择像 3.5（默认）、5.5 这样的值是有用的。
