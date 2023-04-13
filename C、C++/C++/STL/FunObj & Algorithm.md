# 一、函数对象

## 1.1 定义

概念：

- 重载**函数调用操作符**的类，其对象称为函数对象；

- 函数对象使用重载的 `( )` 时，称为**仿函数**，其类称为函数对象类；

- **仿函数**本质是一个类，不是一个函数。

特点：

- 函数对象通常不定义构造函数和析构函数，所以在构造和析构时不会发生任何问题，避免了函数调用的运行时问题；

- 可以定义跨越多次调用的可持久的部分（类似静态局部变量），同时又能够从对象的外面进行初始化和检查（和静态局部变量不同）；

- 可以作为参数传递，也可以有自己的状态（成员函数记录调用情况）。

作用：

- 可以用（而且提倡、建议）函数对象来代替函数指针；

- 模版函数对象使函数对象具有通用性；

- 可以在内部修改，可内联编译，具备有存储先前调用结果的数据成员。

## 1.2 谓词

- 返回 bool 类型的仿函数称为**谓词**；

- 如果一个 operator( ) 接受一个参数则称为一元谓词，两个则为二元谓词；

```cpp
bool operator() (int vall)
{
    return vall > 1;
}

bool operator() (int vall, int val2)
{
    return vall > val2;
}
```

## 1.3 函数对象类模板

    STL 中的函数对象类模板：

| 函数对象类模板           | 成员函数 T operator ( const T & x, const T & y) 的功能   |
| ----------------- | ------------------------------------------------- |
| plus <T>          | return x + y;                                     |
| minus <T>         | return x - y;                                     |
| multiplies <T>    | return x * y;                                     |
| divides <T>       | return x / y;                                     |
| modulus <T>       | return x % y;                                     |
|                   | 成员函数 bool operator( const T & x, const T & y) 的功能 |
| equal_to <T>      | return x == y;                                    |
| not_equal_to <T>  | return x! = y;                                    |
| greater <T>       | return x > y;                                     |
| less <T>          | return x < y;                                     |
| greater_equal <T> | return x > = y;                                   |
| less_equal <T>    | return x <= y;                                    |
| logical_and <T>   | return x && y;                                    |
| logical_or <T>    | return x \| y;                                    |
|                   | 成员函数 T operator( const T & x) 的功能                 |
| negate <T>        | return -x;                                        |
|                   | 成员函数 bool operator( const T & x) 的功能              |
| logical_not <T>   | return !x;                                        |

    示例：

```cpp
// 判断两个 int 变量 x、y 中 x 是否比 y 小，可以写：
if( less<int>()(x, y) ) { ... }
```

## 1.4 内建函数对象

分类：

- 算术仿函数

- 关系仿函数

- 逻辑仿函数

用法：

- #include <functional>

- 所产生的对象，用法和普通函数一致

### 1.4.1 算术仿函数

描述：

- 四则运算的实现；

- negate 取反是一元运算，其余是二元运算；

原型：

- `template<class T> T plus<T>` // 加法仿函数

- `template<class T> T minus<T>` // 减法仿函数

- `template<class T> T multiplies<T>` // 乘法仿函数

- `template<class T> T divides<T>` // 除法仿函数

- `template<class T> T modulus<T>` // 取模仿函数

- `template<class T> T negate<T>` // 取反仿函数

```cpp
plus<int> p;
cout << p(10, 20) << endl;
negate<int> n;
cout << n(50) << endl;
```

### 1.4.2 关系仿函数

描述：实现关系对比；

原型：

- `template<class T> bool equal_to<T>` // 等于

- `template<class T> bool not_equal_to<T>` // 不等于

- `template<class T> bool greater<T>` // 大于

- `template<class T> bool greater_equal<T>` // 大于等于

- `template<class T> bool less<T>` // 小于

- `template<class T> bool less_equal<T>` // 小于等于

```cpp
//sort(v.begin(), v.end(), myCompare());
sort(v.begin(), v.end(), greater<int>());    //greater<int>()内建函数对象
```

### 1.4.3 逻辑仿函数

描述：实现逻辑运算；

原型：

1. `template<class T> bool logical_and<T>` // 逻辑与

2. `template<class T> bool logical_or<T>` // 逻辑或

3. `template<class T> bool logical_not<T>` // 逻辑非

```cpp
    vector<bool>v;
    v.push_back(false);
    v.push_back(true);
    v.push_back(false);
    v.push_back(false);
    v.push_back(true);
    vector<bool> v2;
    v2.resize(v.size());
//有四个参数，分别是原数据的起始位置、原数据的终止位置、目的位置的起始位置、操作运算
transform(v.begin(), v.end(), v2.begin(), logical_not<bool>()); 
```

# 二、常用算法

概述：

- 主要由头文件 `<algorithm>` `<functional>` `<numeric>` 组成；

- `<algorithm>` 是所有 STL 头文件中最大的一个，涉及比较、交换、查找、遍历操作、复制、修改等等；

- `<numeric>` 体积很小，只包括几个在序列绗棉进行简单数学运算的模板函数；

- `<functional>` 定义了一些模板类，用以声明函数对象。

## 2.1 算法分类

根据操作对象：

- 直接改变容器的内容；

- 将原容器的内容复制一份，修改其副本，然后传回该副本。

根据功能：

- 非可变序列算法，指不直接修改其所操作的容器内容的算法；

- 计数算法：count、count_if；

- 搜索算法：search、find、find_if、find_last_of、...；

- 比较算法：equal、mismatch、lexicographical_compare ...；

- 可变序列算法：可以修改所操作的容器内容的算法；

- 删除算法：remove、remove_if、remove_copy ...；

- 修改算法：for_each、transform；

- 排序算法：包括对序列进行排序和合并的算法、搜索算法以及有序序列上的集合操作；

- 数值算法：对容器内容进行数值计算。

## 2.2 常用遍历算法

### 2.2.1 for_each

```cpp
/* 
遍历算法 遍历容器元素 
@param beg 开始迭代器 
@param end 结束迭代器 
@param _callback 函数回调或者函数对象 
@return 函数对象 
*/

for_each(iterator beg, iterator end, _callback); 
```

### 2.2.2 transform

    遍历所有元素，但可对容器的元素进行修改

    作用：

> 可以将一个容器的元素，通过op，变换到另一容器中（同一容器），
> 也可以把两个容器的元素，通过op，变换到另一容器中

    注意：

> 1. 如果目标和源相同，则与 for_each 一样；
> 
> 2. 若以某值替换符合规则的元素，应使用 replace( ) 算法。

```cpp
/*
transform算法 将指定容器区间元素搬运到另一容器中 
注意 : transform 不会给目标容器分配内存，所以需要我们提前分配好内存 
@param beg1 源容器开始迭代器 
@param end1 源容器结束迭代器 
@param beg2 目标容器开始迭代器 
@param _cakkback 回调函数或者函数对象 
@return 返回目标容器迭代器 
*/
transform(iterator beg1, iterator end1, iterator beg2, _callbakc);
```

## 2.3 常用查找算法

### 2.3.1 find

    查找指定元素，找到返回指定元素的迭代器，否则返回结束迭代器 end( )

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param value 查找的元素 
*/

find(iterator beg, iterator end, value);
```

### 2.3.2 find_if

    按条件查找元素。

```cpp
/*
@param first 开始迭代器 
@param last 结束迭代器 
@param pred 函数或谓词（自定义查找规则）
*/

find_if (InputIterator first, InputIterator last, UnaryPredicate pred);
```

### 2.3.3 adjacent_find

    查找相邻重复元素。

```cpp
/*
查找相邻重复元素，返回相邻元素的第一个位置的迭代器
@param beg 开始迭代器 
@param end 结束迭代器 
*/

adjacent_find(iterator beg, iterator end);
```

### 2.3.4 binary_search

    [查找指定元素是否存在](http://c.biancheng.net/view/7537.html)。

```cpp
/*
查找指定元素，存在返回 true，否则返回 false
@param beg 开始迭代器 
@param end 结束迭代器 
@param value 查找的元素
*/

bool binary_search(iterator beg, iterator end, value)
```

### 2.3.5 count

    统计元素个数。

```cpp
/*
统计元素出现次数
@param beg 开始迭代器 
@param end 结束迭代器 
@param value 统计的元素
*/

count(beg, end, value);
```

### 2.3.6 count_if

    按条件统计元素个数。

```cpp
/*
按条件统计元素出现次数
@param beg 开始迭代器 
@param end 结束迭代器 
@param _pred 回调函数或者函数对象 
*/

count_if(beg, end, _pred)
```

## 2.4 常用排序算法

`#include <algorithm>`

### 2.4.1 sort

    [指定范围内进行容器元素排序](http://c.biancheng.net/view/7457.html)。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param _Pred 回调函数或者函数对象 
*/

sort(beg, end, _Pred)
```

> **`std::sort( myvector.begin(), myvector.begin() + 4, std::greater<int>() );`**

### 2.4.2 random_shuffle

    指定范围内的元素随机调整次序。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
*/

random_shuffle(beg, end)
```

### 2.4.3 merge

    合并两个容器的元素，并存储到另一个容器中。

```cpp
/*
容器合并并存储到另一个容器中
注意：两个容器必须是有序的
@param beg1 开始迭代器 
@param end1 结束迭代器 
@param beg2 开始迭代器 
@param end2 结束迭代器 
@param dest 目标容器开始迭代器 
*/

merge(beg1, end1, beg2, end2, iterator dest)
```

### 2.4.4 reverse

    将容器内元素进行反转。

```cpp
/*
反转指定范围的元素
@param beg 开始迭代器 
@param end 结束迭代器 
*/

reverse(beg, end)
```

## 2.5 常用拷贝和替换函数

    #include <algorithm>

### 2.5.1 copy

    容器内指定范围的元素拷贝到另一容器中。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param dest 目标容器开始迭代器 
*/

copy(beg, end, dest)
```

### 2.5.2 replace

    将容器内指定范围的元素替换为新元素。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param oldvalue 旧元素 
@param newvalue 新元素 
*/

replace(beg, end, oldvalue, newvalue)
```

### 2.5.3 replace_if

    将区间内满足条件的元素替换成指定的元素。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param _Pred 回调函数或者函数对象 
@param newvalue 指定的新元素 
*/

sort(beg, end, _Pred， newvalue)
```

### 2.5.4 swap

    互换两个容器的元素。

```cpp
/*
@param c1 容器1 
@param c2 容器2 
*/

swap(container c1, container c2)
```

## 2.6 算术生成算法

- #include <numeric>

- 算术生成算法属于小型算法；

### 2.6.1 accumulate

    计算指定区间内容器元素的总和。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param value 起始值 
*/

accumulate(beg, end, value)
```

### 2.6.2 fill

    向容器中填充指定的元素。

```cpp
/*
@param beg 开始迭代器 
@param end 结束迭代器 
@param value 填充的值 
*/

fill(beg, end, value)
```

## 2.7 常用的集合算法

### 2.7.1 set_intersection

    求两个容器的交集。

```cpp
/*
@param beg1 开始迭代器 
@param end1 结束迭代器 
@param beg2 开始迭代器 
@param end2 结束迭代器 
@param dest 目标容器开始迭代器 
*/

set_intersection(beg1, end1, beg2, end2, iterator dest)
```

### 2.7.2 set_union

    求两个集合的并集。

```cpp
/*
@param beg1 开始迭代器 
@param end1 结束迭代器 
@param beg2 开始迭代器 
@param end2 结束迭代器 
@param dest 目标容器开始迭代器 
*/

set_union(beg1, end1, beg2, end2, iterator dest)
```

### 2.7.3 set_difference

    求两个集合的差集。

```cpp
/*
@param beg1 开始迭代器 
@param end1 结束迭代器 
@param beg2 开始迭代器 
@param end2 结束迭代器 
@param dest 目标容器开始迭代器 
*/

set_difference(beg1, end1, beg2, end2, iterator dest)
```
