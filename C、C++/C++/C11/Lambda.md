## Lambda 表达式

lambda 函数是在调用或作为函数参数传递的位置处定义匿名函数对象的便捷方法。

### 1.1 语法定义

![](D:/Download/MarkText/workspace/image/2022-10-07-11-25-58-image.png)

1. 捕获列表。也称为 Lambda 导入器，[ ] 是 lambda 引出符；

2. 参数列表。若不需要参数传递，可连同“( )”一起省略；

3. 可变规则。默认情况下 lambda 函数是 const 函数，mutable 可取消其常量性，使用该修饰符时参数列表不可省略，即使参数为空；

4. 异常说明。抛出异常；

5. 返回类型。 追踪返回类型形式声明函数的返回类型。在不需要返回值的时候也可以连同符号”->”一起省略。

6. 函数体。可以使用所有捕获的变量。

### 1.2 示例

```cpp
#include <algorithm>
#include <cmath>

void abssort(float* x, unsigned n) {
    std::sort(x, x + n,
        // Lambda expression begins
        [](float a, float b) {
            return (std::abs(a) < std::abs(b));
        } // end of lambda expression
    );
}
```

### 1.3 参数详解

#### 1.3.1 捕获列表

语法上，在“[]”包括起来的是捕获列表，捕获列表由多个捕获项组成，并以逗号分隔。捕获列表有以下几种形式：

- **[ ] 表示不捕获任何变量**

```cpp
auto function = ([]{
        std::cout << "Hello World!" << std::endl;
    }
);
```

- **[ var ] 表示值传递方式捕获变量 var**

```cpp
int num = 100;
auto function = ([num]{
        std::cout << num << std::endl;
    }
);
```

- **[ = ] 表示值传递方式捕获所有父作用域的变量（包括 this ）**

```cpp
int index = 1;
int num = 100;
auto function = ([=]{
            std::cout << "index: "<< index << ", " 
                << "num: "<< num << std::endl;
    }
);
```

- **[ &var ] 表示引用传递捕捉变量 var**

```cpp
int num = 100;
auto function = ([&num]{
        num = 1000;
        std::cout << "num: " << num << std::endl;
    }
);
```

- **[ & ] 表示引用传递方式捕捉所有父作用域的变量（包括 this ）**

```cpp
int index = 1;
int num = 100;
auto function = ([&]{
        num = 1000;
        index = 2;
        std::cout << "index: "<< index << ", " 
            << "num: "<< num << std::endl;
    }
);
```

- **[ this ] 表示值传递方式捕捉当前的 this 指针**

```cpp
#include <iostream>
using namespace std;

class Lambda
{
public:
    void sayHello() {
        std::cout << "Hello" << std::endl;
    };

    void lambda() {
        auto function = [this]{ 
            this->sayHello(); 
        };

        function();
    }
};
```

- **[ =, & ] 拷贝与引用混合**
  
  - [ =, &a, &b ] 表示以引用传递的方式捕捉变量 a 和 b，以值传递方式捕捉其它所有变量。

```cpp
int index = 1;
int num = 100;
auto function = ([=, &index, &num]{
        num = 1000;
        index = 2;
        std::cout << "index: "<< index << ", " 
            << "num: "<< num << std::endl;
    }
);
```

- **[ &, a, this ] 表示以值传递捕捉变量 a 和 this ，引用传递方式捕捉其它所有变量**

- 重复捕捉，编译出错：
  
  ```cpp
  [=, a] 这里已经以值传递方式捕捉了所有变量，但是重复捕捉 a 了，会报错的;
  [&, &this] 这里 & 已经以引用传递方式捕捉了所有变量，再捕捉 this 也是一种重复。
  ```

如果 lambda 主体`total`通过引用访问外部变量，并`factor`通过值访问外部变量，则以下捕获子句是等效的：

```cpp
[&total, factor]
[factor, &total]
[&, factor]
[factor, &]
[=, &total]
[&total, =]
```

#### 1.3.2 参数列表

除了捕获列表之外，还可以接收输入参数（参数列表是可选的）：

```cpp
auto function = [] (int first, int second){
    return first + second;
};

function(100, 200);
```

#### 1.3.3 可变规格 mutable

默认情况下 lambda 函数总是一个 const 函数，mutable 可取消其常量性，使用时参数列表不可为空（即使参数为空）：

```cpp
int m = 0;
int n = 0;
[&, n] (int a) mutable { m = ++n + a; }(4);
```

#### 1.3.4 异常说明

如果 lambda 表达式声明 C4297 异常规范且 lambda 体引发异常，编译器将生成警告 throw( )

```cpp
int main() // C4297 expected 
{ 
     []() throw() { throw 5; }(); 
}
```

在 MSDN 异常规范中明确指出其是 C++11 弃用的语言功能。

### 1.4 优点

- 可以直接在需要调用函数的位置定义函数，而不需要预先定义好函数
  
  ```cpp
  std::find_if(v.begin(), v.end(), [](int& item){return item > 2});
  ```

- 快速封装代码块，结构层次更加明显，代码可读性更好

### 1.5 实现原理

编译器会把一个 lambda 表达式生成一个匿名类的匿名对象，并在类中重载函数调用运算符，实现一个仿函数方法：

```cpp
auto print = []{cout << "Hello World!" << endl; };

转换如下：


class print_class
{
public:
    void operator()(void) const
    {
        cout << "Hello World！" << endl;
    }
};
// 用构造的类创建对象，print 此时就是一个函数对象
auto print = print_class();
```

### 1.6 应用场景

#### 1.6.1 STL 算法库

```cpp
// for_each
a[4] = {11, 2, 33, 4};
sort(a, a+4, [=](int x, int y) -> bool{return x < y};
for_each(a, a+4, [=](int x){cout << x << " ";});
```

```cpp
// find_if
deque<int> coll = {1, 3, 19, 5, 13, 7};
auto pos = find_if(coll.cbegin(), coll.cend(), [=](int i){
   return i > 5 && i < 10; 
});
```

```cpp
// remove_if
std::vector<int> vec_data = {1,2,3,4,5,6}; 
vec_data.erase(
    std::remove_if(vec_data.begin(), vec_data.end()
        , [](int i){
            return n < x;}),
    vec_data.end()
);
```

#### 1.6.2 多线程

```cpp
#include <algorithm>

int main()
{
    std::vector<std::thread> Workers;
    for(int i = 0;i < 5; i++)
    {
        Workers.push_back(std::thread(
            [](){
                std::cout << "main" << std::endl;
            }
        ));
    }

    // [] 告诉编译器正在使用匿名函数
    // &t 赋值一个 task 任务，参数作为线程的引用，并阻塞
    std::for_each(Workers.begin(), Workers.end(),[](std::thread &t){
        t.join();
    });
}
```

#### 1.6.3 函数指针与 Function

```cpp
#include <functional>

 auto add = [](int a, int b) { return a + b; };
 std::function<int(int, int)> Add = [=]{return a+b;}
```

#### 1.6.4 作为函数的入参

```cpp
using FuncCallBack = std::function<void(void)>;

auto callback_handler = [&]{
  std::cout << "handle";  
};

void DataCallBack(FuncCallBack callback)
{
    std::cout << "Start FuncCallback!" << std::endl;
    callback();
    std::cout << "End FuncCallback!" << std::endl;
} 

DataCallBack(callback_handler);
```

#### 1.6.5 QT 消息映射

```cpp
QTimer * timer = new QTimer;
timer->start(1000);
QObject::connect(timer, QTimer::timeout, [&](){
        qDebug() << "lambda" ;
    }
);
```
