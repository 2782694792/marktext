# 2 GenAPI 模块

## 2.1 简介

- GenApi 模块解决如何去配置相机的问题。

> 让相机生产厂商为他们的相机提供机器可以识别的产品说明。这些相机描述文件（camera description files）包含所有需要的信息，用以自动地把相机的属性（features）和其寄存器（registers）相对应。

- 示例：
  
  对相机的 Gain 属性，想令 `Gain=42`，利用 GenICam ，通用软件可以读相机的描述文件并发现，把 Gain 属性设成 42 ，意味着向地址`0x0815 的寄存器写入值 0x2A`。其他要做的工作可能是检查相机是否提供 Gain 属性，并检查要写入的值是否在 Gain 的允许范围内。

- 注意：
  
  给相机添加新的属性仅仅意味着扩展相机的描述文件，就可对所有符号 GenICam 标准的程序立即生效。

<img src="../../../image cache/2022-09-28-15-56-28-image.png" title="" alt="" data-align="center">

## 2.2 节点、接口和抽象特征

    相机描述文件中的每个节点只描述一个项目。

    基于项目的自然性，节点有一个特定的类型（node type）和一个特定的接口（interface）。如下列接口，每个接口都有一个控件用于映射到 GUI ：

```csp
IInteger
    —— 映射到一个带有 value、min、max 和 increment 的 slider
IFloat
    —— 映射到一个带有 value、min、max 和一个物理单位的 slider
IString
    —— 映射到一个显示字符串的编辑框
IEnumeration
    —— 映射到一个下拉框
ICommand
    —— 映射到一个命令按钮
IBoolean
    —— 映射到一个复选框
IRegister
    —— 映射到一个显示 16 进制字符串的编辑框
ICategory
    —— 映射到一个可以反应相机属性结构的树控件
IPort
    —— 映射到一个相机端口，通常不用图形显示
```

    存在多种节点类型实现相同的接口类型。如，`IInteger` 接口被下列节点类型实现：

```cpp
IntReg
    —— 根据节点边界，从寄存器取出一个整数
MaskedIntReg
    —— 从寄存器的一段取出一个整数
Integer
    —— 从不同的节点得到 value、min、max 和 increment 属性，合并在一起
```

    每个节点类型从不同的源，用不同的方法取出一个整数值。对于需要输入一个整数值的连接，所有这些节点的输出值都可以用作类型安全的输入。

    抽象特征（Abstract features）总是用一个接口类型（interface type）、一个名称（name）和一个含义（meaning）来描述。

> 如：相机的 Gain 可以被定义为 IInteger ，并且可以描述相机内的增益。
> 
> 注：其他的定义也可能存在，如被定义为一个 IEnumeration 或一个 IFloat 。

## 2.3 获取和设置值

    当用户读或写一个节点的值，节点会触发节点图内一系列的读写操作。

如下图：Gain 属性可以抽象成一个 IInteger 接口，通过这个接口，用户可以设置 Value 并且可以读（或其它操作）Min 和 Max 值。

> 假定相机3个寄存器，一个是 Gain 的 Value，另两个是 Min 和 Max。利用 IntReg 节点可以从每个寄存器取出相应值。Gain 的 Integer 节点收集并合并这些数据，再通过 IInteger 接口把结果传递出来。

<img src="../../../image cache/2022-09-30-09-51-39-image.png" title="" alt="" data-align="center">

> 若获取 Gain 节点的值，调用会被分派到 GainValue 节点，再由 GainValue 通过 IPort 向 Device 节点查询正确的寄存器；若试图进行设置，则程序会先从 GainMin 和 GainMax 读出范围，若输入值符合，则 Gain 会通过 GainValue 和 Device 节点写入相机。

## 2.4 访问模式

### 2.4.1 模式定义

    每个节点都有一个如下表定义的访问模式：

| Readable | Writable | Implement | Access Mode |
| -------- | -------- | --------- | ----------- |
| *        | *        | 0         | NI - 未实现    |
| 0        | 0        | 1         | NA - 不可用    |
| 0        | 1        | 1         | WO - 只写     |
| 0        | 0        | 1         | RO - 只读     |
| 0        | 1        | 1         | RW - 读写     |

### 2.4.2 访问机制

在运行时改变访问权限的 3 种机制：

1. 属性可能暂时被锁定（locked）。属性在锁定的状态下不可写，写标志位暂时强制为 0；

2. 属性可能暂时不可用（not availabel）。写标志位和读标志位暂时强制为 0；

3. 属性可能没有实现（not implemented）。实现标志位始终强制为 0。

> 以硬件 Trigger 为例来说明让一个属性暂时不可用（temporarily not available）。
> 
>     硬件 Trigger 的值可以为 On 或 Off 。如果是 On，另外一个属性 TriggerPolarity 变可用，TriggerPolarity 属性指示硬件信号是 ActiveHigh 还是ActiveLow。如果 Trigger 是 Off，则 TriggerPolarity 属性无意义，被置为灰色。

<img title="" src="../../../image cache/2022-09-30-10-44-21-image.png" alt="" data-align="center">

    pIsAvailable 可直接指向 TriggerReg ，因为 Trigger = On 被映射为 1，Trigger = Off 被映射为 0。否则一个类型为 IntSwissKnife 的节点，它可以根据数学公式计算其他整数节点的值，并得到一个整数的结果。在 XML 文件中如下：

```xml
<IntSwissKnife Name="TriggerEnabled">
    <ToolTip>Determines if the Trigger feature is switched on</ToolTip>
    <pVariable Name="TRIGGER">TriggerReg</pVariable>
    <Formula>TRIGGER==1</Formula>
</IntSwissKnife>
```

### 2.4.3 暂时锁定属性

    一个典型例子：

    兼容 DCAM 的 1394 相机的 BytesPerPacket 属性是“暂时锁定”（ temporarily locked）的。

> 用户可以改变相机的这个参数，仅当 PC 适配器的 DMA 未被设定为采集图像的时候。设定 DMA 是，传输层查询相机的 BytesPerPacket 参数，并把这个参数设置到 DMA 。完成后，直到传输层释放 DMA 之后才能改变 BytesPerPacket。在这之前，相机的这个参数必须被锁定。

`注意: 相机本身没有办法知道DMA是否设置。因此，相机描述文件中“普通的”节点不能被用`

`来控制 BytesPerPacket 的锁定状态。`

<img src="../../../image cache/2022-09-30-11-18-44-image.png" title="" alt="" data-align="center">

GenApi 内的解决方案是提供一个浮动的 Boolean 型节点 TLParamsLocked 。

> BytesPerPacket 通过 pIsLocked 连接到这个节点。TL 需要通过更新 TLParamsLocked 来反应其 DMA 状态。设置 DMA 之前，通过令 TLParamsLocked 为 true 的方式来锁定相机参数，在采集图像完成之后，会设置回 False。改变 TLParamsLocked 会更新所有关联节点的锁定状态。
> 
> 注意：TLParamsLocked 须是标准节点名，并且 TL 须有访问相机 GenApi 接口的权限。

### 2.4.4 属性没有实现

    （ not implemented）的一个典型例子是，同一家族的某些相机有 Gamma 属性而有些则没有。如果相机有一个查询位（inquiry bit）来标识是否实现了 Gamma 属性，就可以为这个家族的所有相机维护一个相机描述文件。

> Gamma 节点有一个 pIsImplemented 的连接指向 GammaInq 节点，GammaInq 节点映射相机查询位。通常把多个查询位合并到一个寄存器里。为取得这些位，要使用 MaskedIntReg 节点类型。像 IntReg 节点，但是可以像查询整数一样取查询一个或一组连续位。

<img src="../../../image cache/2022-09-30-11-41-52-image.png" title="" alt="" data-align="center">
