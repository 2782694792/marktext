## 创建 SCI 扩展库 DLL 工程的步骤

### 1、创建项目，模板选择 “MFC DLL”

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：13：22.png" title="" alt="" data-align="center">

### 2、选择“使用共享 MFC DLL 的规则”

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：14：46.png" title="" alt="" data-align="center">

### 3、右击“工程”选择“属性”，配置所需“附加包含目录”、“附加依赖项”（参照例程 SciExtDemo 配置路径）

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：16：32.png" title="" alt="" data-align="center">

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：16：57.png" title="" alt="" data-align="center">

### 4、将对应平台下的 def 文件内容拷贝到对应文件。32、64 位工程差异除了引用 lib 有区分，注意 Def 文件不一样

<img src="file:///E:/MarkText/image%20cache/1658715561240.png" title="" alt="" data-align="center">

### 5、添加现有项 SciExtFunc.h 和 .cpp， 只需修改实现以下几个接口函数中的代码，就可实现所需功能的扩展库

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：24：40.png" title="" alt="" data-align="center">

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：27：21.png" title="" alt="" data-align="center">

### 6、如需显示“参数设置对话框的”（即调用 ShowSettingDlg()），需在资源中添加对话框

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：28：53.png" title="" alt="" data-align="center">

<img src="file:///E:/MarkText/image%20cache/Snipaste_07-25&10：29：31.png" title="" alt="" data-align="center">

## x86、x64 平台扩展库 def 内容差异

**（红线部分）**：x64 —— @@QEAA、@AEAV、@@2AEAV

​             x86 —— @@QAE、@AAV、@@2AAV

![](E:\MarkText\image%20cache\Snipaste_07-25&10：36：03.png)

```cpp
--x64:

    Constructor = ??0SciExtFunc@@QEAA@XZ PRIVATE
    Destructor = ??1SciExtFunc@@QEAA@XZ PRIVATE
    ShowSettingDlg = ?ShowSettingDlg@SciExtFunc@@QEAAHAEAVSciImage@@@Z PRIVATE
    ExecuteFunc = ?ExecuteFunc@SciExtFunc@@QEAA_NAEAVSciImage@@AEAVSciVarArray@@AEAVSciCamVariantList@@2AEAVSciOverlayPath@@3@Z PRIVATE
    SetDlgParas = ?SetDlgParas@SciExtFunc@@QEAAXAEAVSciCamVariantList@@@Z PRIVATE
    GetDlgParas = ?GetDlgParas@SciExtFunc@@QEAAXAEAVSciCamVariantList@@@Z PRIVATE 

--x86:

    Constructor = ??0SciExtFunc@@QAE@XZ PRIVATE
    Destructor = ??1SciExtFunc@@QAE@XZ PRIVATE
    ShowSettingDlg = ?ShowSettingDlg@SciExtFunc@@QAEHAAVSciImage@@@Z PRIVATE
    ExecuteFunc= ?ExecuteFunc@SciExtFunc@@QAE_NAAVSciImage@@AAVSciVarArray@@AAVSciCamVariantList@@2AAVSciOverlayPath@@3@Z PRIVATE
    SetDlgParas = ?SetDlgParas@SciExtFunc@@QAEXAAVSciCamVariantList@@@Z PRIVATE
    GetDlgParas = ?GetDlgParas@SciExtFunc@@QAEXAAVSciCamVariantList@@@Z PRIVATE
```

## SciExtFunc.cpp

### 1、ShowSettingDlg( )

**用于显示参数设置对话框，在 SCI 程序设置扩展库界面点击设置按钮时调用**

```C++
/**********************************************************************
 Function: ShowSettingDlg
 Description: 显示参数设置对话框
 Input: SciImage &image    对话框显示的图像，用于测试参数效果
 Return: 0：确定，-1：取消
**********************************************************************/
int SciExtFunc::ShowSettingDlg( SciImage &image )
{

    AFX_MANAGE_STATE(afxGetStaticModuleState());
    AddFunDlg *pDlg = (AddFunDlg *)pData;
    int ret = pDlg->DoModal();

    if (ret == 1)
    {
        return 0; // 确定
    }
    return -1; // 取消

}
```

### 2、ExecuteFunc( )

**接入功能的功能实现函数，每次运行扩展库都会运行该处所实现代码**

```C++
/********************************************************************** 
 Function: ExecuteFunc
 Description: 接入功能的功能实现函数，每次运行扩展库都会运行
 Input: SciImage &image    待处理图像
        SciCamVariantList &inParas 输入参数，参数顺序和值类型
            与 GetDlgParas 获取的顺序和值一样
        SciCamVariantList &outParas 输出结果，顺序自定义，每个结果元素
            必须有独一无二的命名来为以后步骤提供引用
        SciOverlayPath &roi    接入功能的 ROI ，将以蓝色显示
        ScioverlayPath &overlay    接入功能的输出图形，将以绿色显示
***********************************************************************/
bool SciExtFunc::ExecuteFunc( SciImage &image,
        SciCamVariantList &inParas, SciCamVariantList &outParas,
        SciOverlayPath &roi, ScioverlayPath &overlay )
{

    // 先清除内容
    outParas.Reset();
    overlay.Clear();

    //模块切换时的状态保护
    AFX_MANAGE_STATE(afxGetStaticModuleState());

    double num1, num2, num3, result;
    // 获取参数值
    num1 = inParas[0].toInt();
    num2 = inParas[1].toInt();
    num3 = inParas[2].toInt();

    result = num1 + num2 + num3;

    // 将运算结果转换为 SciCamVariant，并添加到 outParas 链表
    SciCamVariant Variant1;
    Variant1.fromDouble(result);
    Variant1.setName("Result");
    outParas.Append(Variant1);

    // ROI 输出
    SciCamVariant Variant2;
    SciROI ROI;
    SciPoint p1, p2;
    ROI.GenRect(p1, p2);
    Variant2.fromROI(ROI);
    Variant2.setName("ROI 输出");
    outParas.Append(Variant2);

    // 添加输出结果图形
    SciPoint(300, 300);
    char resultStr[100];
    sprint_f(resultStr, 100, "%.6f", result);
    overlay.AddText(pt, resultStr);

    // 返回函数处理状态
    return true;

}
```

### 3、SetDlgParas( )

**用于给参数设置对话框的参数赋值，显示参数设置对话框之前会运行该函数。**

```C++
/**********************************************************************
 Function: SetDlgParas
 Description: 用于给参数对话框的参数赋值
 Input: SciCamVariantList &inParas 输入参数，参数顺序和值类型
            与 GetDlgParas 获取的顺序和值一样
**********************************************************************/
void SciExtFunc::SetDlgParas(SciCamVariantList &inParas)
{

    AFX_MANAGE_STATE(afxGetStaticModuleState());

    double num1, num2, num3;
    // 获取参数值
    num0 = inParas[0].toInt();
    num1 = inParas[1].toInt();
    num2 = inParas[2].toInt();

    // 输出到参数设置对话框
    AddFucDlg *pDlg = (AddFucDlg *)pData;
    pDlg->SetParam(num1, num2, num3);

}
```

### 4、GetDlgParas( )

**获取参数，生成参数列表，将每个参数值转换为 SciCamVariant ，并添加到 inParas 链表，以及设置参数是否引用来自 SCI 程序中的数据。通过setEnableDataRef( ) 方法实现，如 Variant1.setEnableDataRef(TRUE) 激活该参数引用 SCI 程序数据功能。在新建扩展库功能步骤，加载库时会运行该函数。以及设置参数对话框显示后，点击确定按钮后也会运行该函数保存更改后的参数。**

```C++
/**********************************************************************
 Function: GetDlgParas
 Description: 用于获取参数，设置参数列表
 Input: SciCamVariantList &inParas 工能参数，每个参数元素需要提供单独的名称
             使参数能够正确保存
**********************************************************************/
void SciExtFunc::GetDlgParas(SciCamVariantList &inParas)
{
    double num1, num2, num3;

    AFX_MANAGE_STATE(afxGetStaticModuleState());
    AddFucDlg * pDlg = (AddFucDlg *)pData;
    pDlg->GetParam(&num1, &num2, &num3);

    inParas.Reset();

    // 生成参数列表，每个参数值转换为 SciCamVariant，命名，
    // 并添加到 inParas 链表
    SciCamVariant Variant1;
    Variant1.fromDouble(num1);
    Variant1.SetName("num1");
    Variant1.SetEnableDataRef(TRUE); // 激活引用功能
    inParas.Append(Variant1);

    // ......

    // ROI 输入
    SciROI ROI;
    SciCamVariant varInput2;
    varInput2.fromROI(ROI);
    varInput2.setEnableDataRef(TRUE);
    inParas.Append(varInput2);
}    
```

## AFX_MANAGE_STATE( AfxGetStaticModuleState( ) )

**作用：用于模块切换时的状态保护**

1. AfxGetStaticModuleState( )：指向当前模块状态；
2. 当前函数调用后原模块的状态会自动被恢复；
3. 用于 DLL 中所调用 MFC 函数、类、资源时的模块状态切换；

```cpp
AFX_MANAGE_STATE(AfxGetStaticModuleState());

#define AFX_MANAGE_STATE(p) AFX_MAINTAIN_STATE2 _ctlState(p);

AFX_MAINTAIN_STATE2::AFX_MAINTAIN_STATE2(AFX_MODULE_STATE* pNewState)
{
    // 当前线程状态获取应用程序状态
    m_pThreadState = _afxThreadState;
    // 保留函数调用后之前的模块状态，以恢复
    m_pPrevModuleState = m_pThreadState->m_pModuleState;
    // 将当前的模块状态进行重新建立
    m_pThreadState->m_pModuleState = pNewState;
}

_AFXWIN_INLINE AFX_MAINTAIN_STATE2::~AFX_MAINTAIN_STATE2()
{
    // 原线程函数调用结束后原模块的状态自动被恢复
    m_pThreadState->m_pModuleState = m_pPrevModuleState; 
}
```

定义一个局部对象，利用其构造和析构函数在函数的入口和出口进行 state 的切换，
而AfxGetStaticModuleState() 是获取当前代码所在 DLL 里的 state；

DLL 里使用 MFC 生成界面，一旦资源放在不同的动态库里，资源加载失败，让 DLL 里的界面代码使用该 DLL 的资源，使得工作线程加载一定的对话框；

> MFC 的对话框装载资源是通过获取当前线程对应的 ModuleState 保存的 ResourceHandler 来装载资源的。所以，DLL 里的代码需要在函数的入口把当前执行线程的 ModuleState 换成该 DLL 里的 State ，这样才能装载该 dll 里的资源。

CDialog 是存放在一个 DLL 里的，自定义的 CDialog 子类若以共享模式连接 MFC 库的话是并没有放在 CDialog 同样的 DLL 里面，

那如何对当前的资源句柄进行传递？1、公共区域；2、参数传递；而 MFC 的很多句柄是保存在全局 Map 里，而且放在线程的私有数据区（不同线程执行同一段代码可以得到不同数据）里。
