# 机制

## DDX 机制

- Dialog Data Exchange (DDX) **对话框数据交换**；

- 在这种机制中，我们先在处理函数 OnInitDialog 或对话框类的构造函数中对对话框对象的成员变量进行初始化，在对话框显示之前，框架的 DDX 机制**将成员变量的值传递给对话框中的控件**。这个过程在成员函数 DoModal 或 Create 被调用的过程中发生；

```cpp
DDX_Text(pDX, IDC_EDIT, m_strEdit); // 数据映射，进行双向关联
```

## DDV 机制

- **对话框数据验证** (dialog data validation)；

- 根据设定的验证规则**验证所有数据项**。

```cpp
DDV_MaxChars(pDX, m_strEdit, 50); // 验证，最大长度为 50
```

# 成员函数

## UpdateData

- **False** : 将**成员变量**的值**传递给对话框**的变量;

- **True** : 将**对话框**的变量的值**传递给对应成员变量**。

> 在数据交换的过程中，成员函数 UpdateData **先创建一个 CDataExchange 对象**，然后调用对话框对类 CDialog 成员函数 DoDataExchange 的重载版本。
> 
> 该 CDataExchange 对象将作为成员函数 DoDataExchange 的一个参数，该参数**定义了数据交换的上下文**。
> 
> 在 DoDataExchange 中，我们**为每一个数据成员指定了一个对 DDX 函数的调用**。每一个函数定义了基于由成员函数 UpdateData 所提供的 CDataExchange 参数所确定的上下文而进行的双向数据交换。

# 数据成员

### m_pMainWnd

> 类 CWinThread 的数据成员 m_pMainWnd 有一个有用的特征，如果由该成员所引用的窗口被关闭的话，MFC 库将自动的终止 CWinThread 对象所代表的线程。

- `删除` **m_pMainWnd = &dlg;**
  
  将指向 dlg 的指针赋予了成员变量 m_pMainWnd，那么，无论我们单击了“确认”还是“取消”，应用程序的主线程都将被自动终止，之后的代码当然不会得到执行。

# 控件类

## 编辑框控件 EDIT

1. 多行文本，自动换行
   
   `设置 Multiline 属性，清除 Auto HScroll 属性`

2. 
