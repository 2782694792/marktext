# OCR

（Optical Character Recognition），光学字符识别，是指使用扫描仪或数码相机等电子设备检查纸上的字符，通过检测暗、亮的方法确定字符的形状，并使用字符识别方法把字符转化为计算机数据的过程；

> 对文本资料进行扫描，然后对图像文件进行图像处理和分析，最终获取文字的过程。

# 示例

```cpp
dev_close_window ()
dev_open_window(0, 0, 600, 600, 'black', WindowHandle)
read_image (Image, 'E:/workspace/Halcon/案例/OCR 识别/20190814180102269.png')
disp_message (WindowHandle, '请绘制单个字体最大区域', 'window', 12, 12, 'blue', 'true')
draw_rectangle1 (WindowHandle, Rowh, Columnh,  Rowh2, Columnh2)
gen_rectangle1 (Rectangleh, Rowh, Columnh,  Rowh2, Columnh2)
fontheight := Columnh2-Columnh
fontweight := Rowh2 - Rowh
dev_clear_window ()
read_image (Image, 'E:/workspace/Halcon/案例/OCR 识别/20190814180102269.png')
* ROI 定位
draw_rectangle1 (WindowHandle,  Row, Column,  Row2, Column2)
gen_rectangle1 (Rectangle, Row, Column,  Row2, Column2)
px := Column + (Column2 - Column)/2
py := Row + (Row2-Row)/2
* ROI 校正
area_center (Rectangle, Area, Row1, Column1)
text_line_orientation (Rectangle, Image, fontheight, -0.523599, 0.523599, OrientationAngle)
*hom_mat2d_identity (HomMat2DIdentity)
*hom_mat2d_rotate (HomMat2DIdentity, -OrientationAngle, px, py, HomMat2DRotate)
vector_angle_to_rigid (Row1, Column1, OrientationAngle, Row1, Column1, 0, HomMat2DRotate)
affine_trans_image (Image, ImageAffineTrans, HomMat2DRotate, 'constant', 'false')
affine_trans_region (Image, RegionAffineTrans, HomMat2DRotate, 'nearest_neighbor')
* 字符分割
reduce_domain (ImageAffineTrans, RegionAffineTrans, ImageReduced1)
dots_image (ImageReduced1, DotImage, 3, 'dark', 0)
threshold (DotImage, Regions, 10, 255)
connection (Regions, ConnectedRegions1)
select_shape (ConnectedRegions1, SelectedRegions2, ['row','column'], 'and', [(Row-fontheight),(Column-fontheight)], [Row2+fontheight,(Column2+fontheight)])
* binary_threshold (DotImage, Region, 'max_separability', 'light', UsedThreshold)
*dilation_rectangle1 (Region, RegionDilation, 5, 5)
* 膨胀闭运算
dilation_rectangle1 (SelectedRegions2, RegionDilation, 1, 2.5)
union1 (RegionDilation, RegionUnion)
gen_rectangle2 (Rectangle3, 30, 32, rad(-45), 0, 1)
closing (RegionUnion, Rectangle3, RegionClosing3)
gen_rectangle2 (Rectangle2, 16, 16, rad(45), 0, 1)
closing (RegionClosing3, Rectangle2, RegionClosing2)
gen_rectangle2 (Rectangle3, 16, 16, rad(0), 0, 1)
closing (RegionClosing2, Rectangle3, RegionClosing1)
connection (RegionClosing1, ConnectedRegions)

select_shape (ConnectedRegions, SelectedRegions, 'area', 'and', 100, fontweight*fontheight)
* 字符训练识别
partition_dynamic(SelectedRegions, Partitioned, 45, 85)
sort_region (Partitioned, SortedRegions,  'character', 'true', 'row')
*intersection (Partitioned, Region, RegionIntersection)
count_obj (SortedRegions, Number)
dev_display (ImageAffineTrans)
Classes := ['2','0','1','6','0','7','1','3','6','G','1','2','A','1','5','3','6','B']
trainfFile := 'E:/workspace/Halcon/案例/OCR 识别/svm.trf'
for i := 1 to Number by 1
    dev_set_color ('red')
    select_obj (SortedRegions, Char, i)
    intersection (Char, Regions, RegionIntersection)
    dev_display (Char)
    * 创建分类器进行训练
    append_ocr_trainf (RegionIntersection, ImageAffineTrans, Classes[i-1], trainfFile)
endfor
* SVM 分类器分类识别
intersection (SortedRegions, Regions, RegionIntersection2)
dev_display (ImageAffineTrans)
dev_set_colored (6)
read_ocr_trainf_names (trainfFile, CharacterNames, CharacterCount)
create_ocr_class_svm (8, 10, 'constant', 'default', CharacterNames, 'rbf', 0.02, 0.001, \
                      'one-versus-one', 'normalization', 10, OCRHandle)
trainf_ocr_class_svm (OCRHandle, trainfFile, 0.001, 'default')
do_ocr_multi_class_svm (RegionIntersection2, ImageAffineTrans, OCRHandle, Class)
smallest_rectangle1 (RegionIntersection2, Row11, Column11, Row22, Column22)
for i := 1 to Number by 1
    disp_message (WindowHandle, Class[i-1], 'image', Row22[i-1]+100, Column11[i-1]+(Column22[i-1]-Column11[i-1])/2, 'red', 'false')
endfor

clear_ocr_class_svm (OCRHandle)
```

# 算子

## text_line_orientation

- `确定文本行或段落的方向`

```cpp
text_line_orientation (ROI_0, Image, 25, -0.523599, 0.523599, OrientationAngle)
```

1. Region 参数指定图像中文本行所在的区域。区域仅用于减少工作面积。为了确定倾斜度，使用该区域内的灰色值；

2. CharHeight 指定区域 region 中现有文本行的大约高度；

3. OrientationFrom 和 OrientationTo 的范围应该在 -pi/4 到 pi/4 的区间内；

4. 计算出的角度 OrientationAngle。

## hom_mat2d_identity

- `生成相同二维变换的齐次变换矩阵`

```cpp
hom_mat2d_identity (HomMat2DIdentity)
```

1. 生成描述相同二维变换的齐次变换矩阵 HomMat2DIdentity

## hom_mat2d_rotate

- `对齐次二维变换矩阵添加一个旋转`

```cpp
hom_mat2d_rotate (HomMat2DIdentity, OrientationAngle, 0, 0, HomMat2DRotate)
```

1. 点(Px,Py)是变换的不动点，即使用HomMat2DRotate进行变换时，该点保持不变；

2. 对齐次二维变换矩阵 HomMat2DIdentity 加上一个角度的旋转，并在HomMat2DRotate中返回得到的矩阵 HomMat2DRotate；

## affine_trans_image

- `对图像应用任意仿射二维变换`

```cpp
affine_trans_image (Image, ImageAffineTrans, HomMat2DRotate, 'constant','false')
```

1. 将输入图像 Image 进行仿射 2D 转换，输出转换后的图像 ImageAffineTrans；

2. 通过输入的 HomMat2DRotate 给出的同次变换矩阵描述，以插值的类型（'bicubic', 'bilinear', 'constant', 'nearest_neighbor', 'weighted'）（默认 constant）对图像结果调整大小确认（默认 false）。

## dots_image

- `点图像`

```cpp
dots_image (ImageAffineTrans, DotImage, 11, 'dark', 0)
```

1. 增强输入图像中直径的圆形圆点；

2. 将输入图像进行点圆形直径增加 Diameter （默认为 5）；

3. FilterType 选择图像中的“暗点”、“亮点”或“所有”点是否应该增强；

4. PixelShift 既可以用来增加输出图像的对比度(PixelShift > 0)，也可以用来抑制将被切断的极亮区域的值(PixelShift = -1)。

## reduce_domain

- `缩小图像的域`

```cpp
reduce_domain (DotImage, ROI_0, ImageReduced)
```

1. 将给定图像的定义域缩小到指定的区域；

2. 新的定义域计算为旧定义域与区域的交集。

3. 新的定义域可以是区域的子集。矩阵的大小没有改变。

## vector_angle_to_rigid

- `从点和角度计算刚性仿射变换`

```cpp
vector_angle_to_rigid (Row1,Column1,OrientationAngle, Row1,Column1,0, HomMat2D)
```

1. 计算一个刚性仿射变换，即一个由旋转和平移组成的变换，从一个点对应关系和两个对应角度出发，返回齐次变换矩阵 HomMat2D；

2. 原点的坐标 (Row1,Column1) 和对应角度 OrientationAngle，转换点的坐标 (Row2,Column2)和对应的角度 Angle2。

## binary_threshold

- `使用二进制阈值分割图像`

```cpp
binary_threshold (DotImage, Region, 'max_separability', 'light', UsedThreshold)
```

1. 使用自动确定的全局阈值分割单通道图像，并在 region 中返回分割的区域；

2. 'max_separability'和'smooth_histo'。这两种方法只能用于具有双峰直方图的图像；

3. 分割方法 'smooth_histo' 提供的功能与操作符 bin_threshold 提供的功能相同；

4. 分割方法 'max_separability' 倾向于为 UsedThreshold 确定较小的值；

5. 'LightDark' 为提取的前景或背景（默认 'dark'）。

## dilation_rectangle1

- `使用矩形结构元素扩展区域`

```cpp
dilation_rectangle1 (Region, RegionDilation, 11, 11)
```

1. 构造矩形的大小为宽度×高度，结果是区域被放大，区域内部小于矩形掩模的孔被关闭；

## union1

- `返回所有输入区域的联合`

```cpp
union1 (RegionDilation, RegionUnion)
```

1. RegionUnion <= Region ： RegionUnion <= RegionDilation。

## shape_trans

- `变换区域的形状`

```cpp
shape_trans (ConnectedRegions, RegionTrans, 'rectangle1')
```

1. shape_trans 根据参数 Type 转换输入区域的形状。

## partition_rectangle

- `将一个区域划分成大小大致相等的矩形`

```cpp
partition_rectangle (SelectedRegions, Partitioned, 45, 85)
```

1. partition_rectangle 将输入区域划分为宽度乘以高度的矩形。矩形在 Partitioned 中返回。区域总是被分割成大小大致相等的矩形。

## intersection

- `计算两个区域的交点`

```cpp
intersection (Partitioned, Region, RegionIntersection)
```

1. 计算区域 Partitioned 中的区域与区域 Region 中的区域的交集；

## partition_dynamic

- `在垂直范围较小的位置上水平划分区域`

```cpp
partition_dynamic(SelectedRegions, Partitioned, 45, 85)
```

1. 将输入区域水平划分为距离宽度近似的区域，当输入区域宽度大于1.5倍距离时，才进行分区；

2. Distance 为所得到的的结果区域部分的近似宽度；

3. Percent 分割位置的最大移动百分比。

## sort_region

- `根据区域的相对位置对区域进行排序`

```cpp
sort_region (Partitioned, SortedRegions, 'first_point', 'true', 'row')
```

1. SortMode 排序模式：'character', 'first_point', 'last_point', 'lower_left', 'lower_right', 'upper_left', 'upper_right'，默认为 'first_point'；

2. Order 递增（true）或递减（false）排序顺序，按行（row）或列（column）进行排序。

## append_ocr_trainf

- `将字符添加到训练文件`

```cpp
append_ocr_trainf (RegionIntersection,ImageAffineTrans, Classes[i-1],'/svm.rtf')
```

1. 操作符 append_ocr_trainf 用于使用操作符 trainf_ocr_class_mlp 或 trainf_ocr_class_svm 来准备训练；

2. 表示字符的区域，包括其灰度值(区域和像素)和对应的类名将被写入文件（追加写）；

3. character 中每个字符(区域) RegionIntersection 必须在 class 中指定相应类名 Classes；

4. 灰度值通过参数 Image （ImageAffineTrans）传递。

## read_ocr_trainf_names

- `查询那些字符存储在训练文件中`

```cpp
read_ocr_trainf_names (trainfFile, CharacterNames, CharacterCount)
```

1. 从训练文件（.trf, .otr）中读取字符名称数组和对应的字符数量数组。

## create_ocr_class_svm

- `使用支持向量机创建一个OCR分类器`

```cpp
create_ocr_class_svm (8, 10, 'constant', 'default', CharacterNames, \
                      'rbf', 0.02, 0.001, 'one-versus-one', 'normalization', \
                      10, OCRHandle)
```

1. 分割字符的灰度值被放大到的矩形的宽度 WidthCharacter（默认 8）、高度（默认 10）；

2. Interpolation 用于字符缩放的插值模式（'bicubic', 'bilinear', 'constant'(默认), 'nearest_neighbor', 'weighted'）；

3. Features 用于分类的特征（默认 'default'）；

4. CharacterNames 要读取的字符集的所有字符，内核类型默认 'rbf'（ 'linear', 'polynomial_homogeneous', 'polynomial_inhomogeneous', 'rbf'）；

5. 内核函数的附加参数（默认 0.02），SVM的正则化常数（0.001），SVM 的模式（'one-versus-all', 'one-versus-one'）；

6. Preprocessing 用于变换特征向量的预处理类型（'canonical_variates', 'none', 'normalization', 'principal_components'）；

7. 预处理参数NumComponents : 转换后的特征的数量( Preprocessing = 'none' 和 'normalization' 时忽略)，默认 10。

## trainf_ocr_class_svm

- `训练一个 OCR 分类器`

```cpp
trainf_ocr_class_svm (OCRHandle, trainfFile, 0.001, 'default')
```

1. 用 TrainingFile 给出的 OCR 训练文件 trainfFile 中存储的训练字符训练 OCR 分类器OCRHandle；

2. 训练停止参数（默认 0.001），训练模式（'add_sv_to_train_set', 'default'）。

## do_ocr_multi_class_svm

- `使用基于 SVM 的 OCR 分类器对多个字符进行分类`

```cpp
do_ocr_multi_class_svm (RegionIntersection2, ImageAffineTrans, OCRHandle, Class)
```

1. 准备识别的字符 RegionIntersection2，字符的灰度值 ImageAffineTrans，分类结果 Class。
