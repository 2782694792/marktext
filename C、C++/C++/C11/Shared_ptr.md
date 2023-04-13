> 智能指针：可以在适当时机进行自动释放分配的内存。
> 
>         垃圾回收机制：使用智能指针可以很好地避免“忘记释放内存而导致内存泄漏”。
> 
>         每种智能指针都是以类模板的方式实现的。

# Shared_ptr 共享指针

## 定义

- `共享指针`，即多个指针指向同一个内存；底层是采用`引用计数`的方式实现的。

> 智能指针在申请内存空间的同时，会为其配备一个整形值（初始值为 1 ），每当有新对象使用此堆内存时，该值 +1 ；反之，每当使用此堆内存的对象被释放时，该整形值 -1 。当堆空间对应的整形值为 0 时，表明不再有对象使用它，该堆空间就会被释放掉。

- #include <memory>    using namespace std;

## 特征

- 智能指针实质是重载了 -> 和 * 操作符的类，由类来实现对内存的管理，确保即使有异常产生，也可以通过智能指针类的析构函数完成内存的释放；

- 利用 C++ 的 RAII 和 引用计数技术：
  RAII ：保证在任何的情况下，使用对象时先构造对象，最后析构对象；
  引用计数 ：通过计算对裸指针引用次数来决定是否要释放这个指针对象；

## 区别

和 unique_ptr、weak_ptr 不同之处在于：

- 多个 shared_ptr 智能指针可以共同使用同一块堆内存。并且，由于该类型智能指针在实现上采用的是引用计数机制，即便有一个 shared_ptr 指针放弃了堆内存的“使用权”（引用计数减 1），也**不会影响其他指向同一堆内存的 shared_ptr 指针**。

## 声明创建

### 1、空智能指针

空的 shared_ptr 指针，其初始引用计数为 0

```cpp
std::shared_ptr<int> p1;             // 不传入任何实参
std::shared_ptr<int> p2(nullptr);    // 传入空指针 nullptr
```

### 2、定义指向、初始化

```cpp
std::shared_ptr<int> p3(new int(10)); // 等同初始化
```

std::make_shared<T> 模板函数，其可以用于初始化 shared_ptr 智能指针

```cpp
std::shared_ptr<int> p3 = std::make_shared<int>(10);
```

### 3、构造函数

```cpp
// 调用拷贝构造函数
std::shared_ptr<int> p4(p3); // p3 是左值，被引用，计数 +1
// 调用移动构造函数
std::shared_ptr<int> p5(std::move(p4)); // 强制转换 p4 为右值
// 或者 std::shared_ptr<int> p5 = std::move(p4);
```

> 和调用拷贝构造函数不同，用 std::move(p4) 初始化 p5，
> 会`使得 p5 拥有了 p4 的堆内存，而 p4 则变成了空智能指针`。

### 4、异常指向

同一普通指针不能同时为多个 shared_ptr 对象赋值，否则会导致程序发生异常。

```cpp
int* ptr = new int;
std::shared_ptr<int> p1(ptr);
std::shared_ptr<int> p2(ptr); // 错误
```

### 5、自定义释放规则

**删除器**：对于申请的动态数组来说，shared_ptr 指针默认的释放规则是不支持释放数组的，**只能自定义对应的释放规则**，才能正确地释放申请的堆内存。

> 对于申请的动态数组，释放规则可以使用 default_delete<T> 模板类

```cpp
// 1. 指定 default_delete 作为释放规则
std::shared_ptr<int> p6(new int[10], std::default_delete<int[]>());

// 2. 自定义释放规则
void deleteInt(int*p) {
    delete []p;
}
// 初始化智能指针，并自定义释放规则
std::shared_ptr<int> p7(new int[10], deleteInt);

// 3. lambda
std::shared_ptr<int> p7(new int[10], [](int * p){
    delete []p;
});
```

## 成员方法

| operator=        | 重载赋值号，使得同一类型的 shared_ptr 智能指针可以相互赋值                                                                                   |
| ---------------- | --------------------------------------------------------------------------------------------------------------------- |
| operator*        | 获取当前 shared_ptr 智能指针对象指向的数据                                                                                           |
| operator->( )    | 当智能指针指向的数据类型为自定义的结构体时，通过 -> 运算符可以获取其内部的指定成员                                                                           |
| swap             | 交换 2 个相同类型 shared_ptr 智能指针的内容                                                                                         |
| reset            | 当函数没有实参时，当前 shared_ptr 所指堆内存的引用计数减 1，同时将当前对象重置为一个空指针；当为函数传递一个新申请的堆内存时，则调用该函数的 shared_ptr 对象会获得该存储空间的所有权，并且引用计数的初始值为 1 |
| get              | 获得 shared_ptr 对象内部包含的普通指针                                                                                             |
| use_count        | 返回同当前 shared_ptr 对象（包括它）指向相同的所有 shared_ptr 对象的数量                                                                      |
| unique           | 判断当前 shared_ptr 对象指向的堆内存，是否不再有其它 shared_ptr 对象再指向它                                                                    |
| operator bool( ) | 判断当前 shared_ptr 对象是否为空智能指针，如果是空指针，返回 false；反之，返回 true                                                                 |

## 示例

```cpp
#include <iostream>
#include <memory>

struct BigObj {
    BigObj() {
        std::cout << "big object has been constructed" << std::endl;
    }
    ~BigObj() {
        std::cout << "big object has been destructed" << std::endl;
    }
};

void test_ref() {
    std::shared_ptr<BigObj> sp1 = std::make_shared<BigObj>();
    std::cout << sp1.use_count() << std::endl;
    std::shared_ptr<BigObj> sp2 = sp1;
    std::cout << sp2.use_count() << std::endl;
    std::shared_ptr<BigObj> sp3 = sp2;
    std::cout << sp3.use_count() << std::endl;
    std::cout << sp1.use_count() << std::endl;
}

void test_ref1() {
    std::shared_ptr<BigObj> sp1 = std::make_shared<BigObj>();
    std::cout << sp1.use_count() << std::endl;

    {
        std::shared_ptr<BigObj> sp2 = sp1;
        std::cout << sp1.use_count() << std::endl;
    }
    std::cout << sp1.use_count() << std::endl;
    BigObj* ptr = sp1.get();

    sp1 = nullptr;
    std::cout << sp1.use_count() << std::endl;
}

int main() {

    test_ref();
    std::cout << std::endl;
    test_ref1();

}
```

```shell
big object has been constructed
1
2
3
3
big object has been destructed

big object has been constructed
1
2
1
big object has been destructed
0
```

## 注意事项

1. 不要把一个原生指针给多个 shared_ptr 管理；

2. 不要把 this 指针给 shared_ptr ；

3. 不要在函数实参里创建 shared_ptr ；

4. 不要直接把指针替换为 shared_ptr 来防止内存泄漏；

5. 环状的链式结构 shared_ptr 将会导致内存泄漏(可以结合 weak_ptr 来解决)；

6. 多线程环境中使用共享指针的代价非常大，需要避免关于引用计数的数据竞争；

7. 共享对象的析构器不会在预期的时间执行；

8. 不使用相同的内置指针值初始化(或 reset )多个智能指针；

9. 不 delete get( ) 返回的指针；

10. 不使用 get( ) 初始化或 reset 另一个智能指针；

11. 使用 get( ) 返回的指针，记住当最后一个对应的智能指针销毁后就变为无效了；

12. 使用智能指针管理的资源不是 new 分配的内存，记住传递给它一个删除器。

# 问题

## 共享指针是怎么计数的

[面试问题记录：c++中共享指针是怎样计数的？_柯西等式的博客-CSDN博客_共享指针计数](https://blog.csdn.net/zhangruijerry/article/details/100927531)
