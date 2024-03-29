[CPPInterview/C++/CPPBasic.md at master · SYaoJun/CPPInterview · GitHub](https://github.com/SYaoJun/CPPInterview/blob/master/C%2B%2B/CPPBasic.md)

## 面向对象三大特征

- 封装：通过访问控制使得数据隐藏，同时可以进行接口和属性限制，防止使用者错误修改内在属性，有助于对象实现对细节的整理；
- 继承：使得派生类具有基类的共有域和方法，使得拥有基类相同的行为；
- 多态：不同的基类具有不同的行为表现，使得类处理、描述多样化。

## 多态

- 同一类型的对象在不同的状态下，表现出不同的行为；
- 实现：继承、虚函数和指针、重载、接口（java）；
- **静态**多态：重载和模板；
- **动态**多态：运行时多态，通过继承和虚函数实现；
- 实现**核心：虚函数表指针**。

## 虚函数表

- VTable，**存放虚函数地址的表格**；
- C++ 用来实现虚函数的**动态绑定和多态**；
- 每一个包含虚函数的类都会有自己的虚函数表，存储了类中所有虚函数的地址；
- **子类重写**虚函数，会对虚函数的**入口地址进行替换**；
- 利：扩展程序；弊：**每个对象多了一个 4 字节的指针**，每次查询虚函数表需要耗时。

## 函数与变量

### 内联函数

- 如果一个函数是内联的，那么在**编译**时，编译器会把该函数的**代码副本**放置在每个调用该函数的地方，**空间换时间**；
- 对内联函数进行任何修改，都需要重新编译函数的所有客户端，因为编译器需要重新更换一次所有的代码；
- 内联函数内**不允许使用循环语句和开关语句**；
- 内联函数的定义必须出现在内联函数第一次调用之前；
- 类结构中所在的类说明内部定义的函数是内联函数。

### 静态全局和全局

静态全局变量：

- 在**全局数据区分配内存**，用 static 修饰；
- 未经初始化的静态全局变量会被程序**自动初始化**为 0；
- **仅当前文件可见**；
- 静态成员函数，所有对象共享同一个函数，只能访问静态成员变量。

全局变量：

- 默认是动态的；**作用域是整个工程**；
- 在一个文件内定义的全局变量，在另一个文件中，通过 **extern** 全局变量名的声明，就可以**使用全局变量**。

## 指针和引用

**指针**：保存所指**对象的地址**；可以有**多级**指针；通过**解引用间接访问**对象的值；可不初始化。

**引用**：是所指**对象的别名**；最多**两级**引用；**直接访问**对象的值；**必须初始化**；本质是**指针常量**，只可以修改指向的值。

## RAII

- Resource Acquisition Is Initialization，**资源获取即初始化**，用于管理动态分配的资源，例如堆内存、文件句柄、网络连接等；
- 核心思想：通过在对象的**构造函数中获取资源**，在**析构函数中释放资源**，从而确保资源的正确管理和释放；
- 实现 RAII 的关键：**将资源的生命周期与对象的生命周期绑定在一起**。当对象被创建时，它会自动获取资源，当对象被销毁时，它会自动释放资源。

## RTII

- run time type identification，**运行时类型识别**；
- 常常结合 **typeid**() 和 **dynamic_cast** 实现。可根据当前调用的指针是何种类型，经过 dynamic_cast 转换调用非虚函数；
- dynamic_cast **只能用于指针和引用的转换**，要转换的类型中**必须包含虚函数**，转换成功**返回子类的地址**，失败返回 NULL；
- typeid 返回一个 type_info 对象的引用。

## struct 与 class

1. 共同点：C++ 中，可以用 struct 和 class 定义类，**都可以继承**；
2. 不同点：**struct** 默认继承权限、访问权限为 **public**；**class** 默认继承权限、访问权限为 **private**。

## const 和 define

- **const** 可以**明确指定数据类型**，而宏定义没有数据类型。
- define **宏是在预处理阶段展开**，**const 常量是在编译运行阶段**使用。
- define **宏不分配内存**，变量定义分配内存。

## DEBUG 与 RELEASE 

1、DEBUG：

- 调试版本，包含调试信息，容量比 Release 大很多，并且不进行任何优化；

- Debug 模式下生成两个文件，除了.exe 或 .dll 文件外，还有一个 .pdb 文件，该文件记录了代码中断点等调试信息；

2、RELEASE：

- 发布版本，不对源代码进行调试，编译时对应用程序的速度进行优化，使得程序在代码大小和运行速度上都是最优的；

- Release 模式下生成一个文件 .exe 或 .dll 文件；
