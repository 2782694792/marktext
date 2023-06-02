# 机制

## DDX 机制

- Dialog Data Exchange (DDX) **对话框数据交换**；

- 在这种机制中，我们先在处理函数 OnInitDialog 或对话框类的构造函数中对对话框对象的成员变量进行初始化，在对话框显示之前，框架的 DDX 机制**将成员变量的值传递给对话框中的控件**。这个过程在成员函数 DoModal 或 Create 被调用的过程中发生；

```php
DDX_Text(pDX, IDC_EDIT, m_strEdit); // 数据映射，进行双向关联
```

## DDV 机制

- **对话框数据验证** (dialog data validation)；

- 根据设定的验证规则**验证所有数据项**。

```php
DDV_MaxChars(pDX, m_strEdit, 50); // 验证，最大长度为 50
```

# 成员函数

## UpdateData

- **False** : 将**成员变量**的值**传递给对话框**的变量;

- **True** : 将**对话框**的变量的值**传递给对应成员变量**。
1. 在数据交换的过程中，成员函数 UpdateData **先创建一个 CDataExchange 对象**，然后调用对话框对类 CDialog 成员函数 DoDataExchange 的重载版本。

2. 该 CDataExchange 对象将作为成员函数 DoDataExchange 的一个参数，该参数**定义了数据交换的上下文**。

3. 在 DoDataExchange 中，我们**为每一个数据成员指定了一个对 DDX 函数的调用**。每一个函数定义了基于由成员函数 UpdateData 所提供的 CDataExchange 参数所确定的上下文而进行的双向数据交换。

# 数据成员

## m_pMainWnd

类 CWinThread 的数据成员 m_pMainWnd 有一个有用的特征，如果由该成员所引用的窗口被关闭的话，MFC 库将自动的终止 CWinThread 对象所代表的线程。

- 删除**m_pMainWnd = &dlg;**将指向 dlg 的指针赋予了成员变量 m_pMainWnd，那么，无论我们单击了“确认”还是“取消”，应用程序的主线程都将被自动终止，之后的代码当然不会得到执行。

# 消息

## 三种消息

### 窗口消息 WM_XXX

- **一般与窗口的内部运作有关**，如：创建窗口、绘制窗口和销毁窗口等。通常，消息是从系统发送到窗口，或从窗口发送到窗口。

### 命令消息 WM_COMMAND

- **一般与处理用户请求相关**，当用户单击一个菜单项或工具栏时，命令消息产生，并被发送到能处理该请求的类对象(如：装载文件、编辑文本和保存选项等)。

### 控件通知

- **在某些重要事件发生时**，由控件窗口发送到父窗口，如打开一个组合框。控件通知为父窗口进一步控制子窗口提供了机会。例如，打开一个组合框时，父窗口可以用组合框初建时得不到的消息填充它；

- BN_XXXX 是 CButton 产生的消息，EN_XXXX 是 CEdit 产生的消息，等等；

- 子窗口控件（Predefined Chilld Window Controls）包括 BUTTON、COMBOBOX、EDIT、LISTBOX、RichEdit、SCROLLBAR、STATIC。

## 消息映射

### 具体实现方法

- `在每个能接收和处理消息的类中，定义一个消息和消息响应函数的**静态对照表**，即**消息映射表**；`

- `在消息映射表中，**消息与对应的消息处理函数指针是成对出现的**。`

- `某个类能处理的所有消息及其对应的消息处理函数的地址都列在这个类所对应的静态表中；`

- `当有消息需要处理时，程序只要搜索该消息静态表，查看表中是否含有该消息，就可以知道该类能否处理此消息；如果能处理，则同样依照静态表找到并调用对应的消息处理函数。`

### 相关信息

1. 消息响应函数的**声明**；

2. 消息响应函数**实现**；

3. 用来关联消息和消息响应函数的**宏**。

### 消息映射宏

1. **Windows 消息映射宏** 用于处理普通的窗口消息。此类消息映射宏前缀为“ON_WM_”，并且没有参数；

2. **命令消息映射宏** 命令消息 WM_COMMAND 是一种特殊的窗口消息，它从一个窗口发送到另一个窗口，以处理来自用户的请求，是 ON_COMMAND 宏和 ON_COMMAND_RANGE 宏；

3. **控件通知消息映射宏** 控件通知消息是指控件窗口发送到其父窗口的消息，其消息映射宏为 ON_CONTROL 和 ON_CONTROL_RANGE，有时在程序中并不见 ON_CONTROL 宏，而见 ON_BN_CLICKED 宏， 其实 ON_CONTROL 宏派生出许多的映射宏，包括 ON_BN_CLICKED 宏等；

4. **窗口通知消息映射宏** 窗口通知消息映射宏处理的窗口消息有 WM_COMMAND，WM_NOTIFY，其消息映射宏有 ON_NOTIFY 和 ON_NOTIFY_RANGE；

5. **反射消息映射宏** 反射消息是指子窗口向父窗口发送的通知消息或控件通知消息，父窗口将该消息转化为相应的反射消息发送给子窗口优先处理。 处理窗口消息 WM_NOTIFY+WM_REFLECT_BASE 的宏是 ON_NOTIFY_REFLECT，处理窗口消息 WM_COMMAND+WM_REFLECT_BASE 的宏是 ON_CONTROL_REFLECT， 还有其他的反射消息宏，具体参考 MDSN；

6. **扩展消息映射宏** 有 ON_COMMAND | ON_COMMAND_RANGE | ON_NOTIFY | ON_NOTIFY_RANGE 等。

### 自定义消息

#### **使用 #define 定义消息 ID**

1. 自定义消息：#define **WM_CCTRY_MSG** (WM_USER+100)；

> 注意：自定义效益都要大于0x0400，如果小于0x0400，则消息为系统消息。一般定义的时候都是WM_USER+ * 定义消息ID。

2. 头文件中添加消息响应函数声明：afx_msg LRESULT OnCcTryMsg(WPARcAM wParam, LPARAM lParam);

3. CPP文件中添加消息响应函数实现： LRESULT CDlgTestDlg::OnCcTryMsg(WPARAM wParam, LPARAM lParam) { //相关代码； }

4. 在 BEGIN_MESSAGE_MAP 与 END_MESSAGE_MAP 之间加入消息的映射代码：**ON_MESSAGE**(**WM_CCTRY_MSG**, &CDlgTestDlg::OnCcTryMsg)

#### RegisterWindowMessage 函数注册 ID

1. 使用自定义消息的类中定义一个**注册的字符串** ：#define MY_REGISTERED_MSG_STR _T("MY_MSG_STRING")

2. 定义一个**UINT型的ID** ：UINT WM_MY_REGISTERED_MSG;

3. 使用 **RegisterWindowMessage（定义的字符串）函数注册 ID**，返回值为消息映射的 ID ： WM_MY_REGISTERED_MSG =RegisterWindowMessage(MY_REGISTERED_MSG_STR);

4. **CPP文件中创建**消息映射函数： **ON_REGISTERED_MESSAGE**(WM_MY_REGISTERED_MSG,&CDlgTestDlg::Onddd);

5. **头文件中定义**消息映射函数：afx_msg LRESULT Onddd(UINT nID, LPARAM lParam);

6. **CPP 文件中实现**消息映射函数。

## 消息反射

- 父窗口将控制子窗口发给它的通知消息，首先反射回子窗口进行处理（即给控制子窗口一个机会，让控制子窗口处理此消息），这样通知消息就有机会能被子窗口自身进行处理；

- 可以使得控制子窗口能够自行处理与自身相关的一些消息，增强了封装性，从而提高了控制子窗口的可重用性。
