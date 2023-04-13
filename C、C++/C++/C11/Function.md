## std::function

### 定义

类模板 std::function 是一种通用的、多态的函数封装。

> std::function 的实例可以对任何可以调用的目标实体进行存储、复制和调用操作，这些目标实体包括普通函数、Lambda 表达式、函数指针以及其他函数对象等。

std::function 对象是对 C++ 中现有的可调用实体的一种类型安全的包裹（像函数指针这类可调用实体，是类型不安全的）。

通常 std::function 是一个函数对象类，它包装其它任意的函数对象，被包装的函数对象具有类型为 T1, ..., TN 的 N 个参数，并且返回一个可转换到 R 类型的值。std::function 使用模板转换构造函数接收被包装的函数对象；特别是，闭包类型可以隐式地转换为 std::function。

### 实现原理

1. 通过类的多态，即通过虚表来达到多态；

2. 通过 C 语言的函数指针来实现；

单参数类模板，是 function<int(int)> 或 function<String(int)> 等格式的类模板：

```cpp
template<class R, typename Arg0>
class myfunction<R(Arg0)>
{
    public:
        R operator()(Arg0 arg0)
        {
            return ...;
        }   
}
```

若实现可以调用不同的函数，模板函数首先要有一个指针，而且该指针具有多态性（C++ 通过基继承和虚表），故需要有一个基类指针使得所有继承于该类的子类成员，使用 `virtual` 关键字创建虚表：

```cpp
template<class R, typename Arg0>
class myfunction<R(Arg0)> function 
{
    private:
        class __callbase{
            public:
                virtual R operator()(Arg0 arg0) = 0;
                virtual ~__callbase(){ }
        };

        __callbase *base_;

    public:
        R operator()(Arg0 arg0){
            return (*__callbase)(arg0);
    }
};
```

实现子类：

```cpp
class myfunction<R(Arg0)> function{
    private:
        template<typename F>
        class __callable : public __callbase {
            public:
                // 增加指向赋值给 function 类的函数指针或函数类对象
                callable(F functor)
                    : functor(functor){ }
                // 指向后重载操作符进行直接调用
                virtual R operatr()(Arg0 arg0){
                    return functor(arg0);
                }
            private:
                // 指向具体的函数或函数类对象，由模板推导
                F functor;
        };
    public:
        template<typename F>
        myfunction(F f):base_(new __callable<F>(f)){
        }

        ~myfunction(){
            if(base_){
                delete base_;
                base_ = nullptr;
            }
        }
};
```

## 应用

**`通过 std::function 对 C++ 中各种可调用实体（普通函数、Lambda 表达式、函数指针、
仿函数以及其它函数对象等）的封装，形成一个新的可调用的、统一的 std::function 对象。`**

```cpp
#include <functional>
#include <iostream>
using namespace std;

std::function<int(int)> Functional;


int TestFunc(int a){ return a;}// 普通函数

auto lambdaFn = [](int a)->int{ return a; };// Lambda表达式

class Functor{ // 仿函数(functor)
 public:
    int operator()(int a){return a;}
};

class TestClass{
 public:
    int ClassMember(int a) { return a; }// 1.类成员函数
    static int StaticMember(int a) { return a; }// 2.类静态函数
}; 


int main()
{

    // 普通函数
    Functional = TestFunc;
    int result = Functional(10);
    cout << "普通函数："<< result << endl;


    // Lambda表达式
    Functional = lambdaFn;
    result = Functional(20);
    cout << "Lambda表达式："<< result << endl;


    // 仿函数
    Functor testFunctor;
    Functional = testFunctor;
    result = Functional(30);
    cout << "仿函数："<< result << endl;

    // 类成员函数
    TestClass testObj;
    Functional = std::bind(&TestClass::ClassMember, testObj
                    , std::placeholders::_1);
    result = Functional(40);
    cout << "类成员函数："<< result << endl;


    // 类静态函数
    Functional = TestClass::StaticMember;
    result = Functional(50);
    cout << "类静态函数："<< result << endl;
    return 0;

}
```

### 注意事项

- 关于可调用实体转换为 std::function 对象需要遵守以下两条原则：
  
  - 转换后的 std::function 对象的参数能转换为可调用实体的参数；
  
  - 可调用实体的返回值能转换为 std::function 对象的返回值。

- std::function 对象最大的用处就是在实现函数回调，使用者需要注意，它不能被用来检查相等或者不相等，但是可以与 NULL 或者 nullptr 进行比较。
