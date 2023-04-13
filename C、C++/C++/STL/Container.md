# Vector

- #include<vector>    using namespace std; 

- 矢量容器 ：单向开口的连续内存空间，底层封装：数组；

- 预留内存空间不够时，可以动态扩展（容器满了则新建容器同时进行拷贝）；

- 顺序序列：容器元素按照严格的线性顺序排列；

- 动态数组：支持对序列中任意元素进行快速直接访问，高效的随机访问的容器；

- 越靠后的插入或删除执行效率越高，删除不会释放限制的空间；

- 拥有一段连续的内存空间，支持随机存取，但在中间进行插入和删除会造成内存块的拷贝；

- [Vector 接口函数介绍](https://vimsky.com/examples/usage/cpp_standard_library-cpp_vector_get_allocator-03.html)。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-01-10-59-25-image.png" title="" alt="" data-align="center">

## Capacity

| Name          | Role                            | Notice                            |
| ------------- | ------------------------------- |:---------------------------------:|
| size          | 返回矢量实际存储元素数目（实际数据量）             |                                   |
| max_size      | 返回矢量所能存储的最大的元素数目                |                                   |
| resize        | 更改矢量实际存储，可自定义默认值填充              | resize(num)<br/>resize(num, elem) |
| capacity      | 不分配内存的情况下可以存储的最多元素个数            |                                   |
| empty         | 判断矢量存储是否为空                      |                                   |
| reserve       | 更改矢量 capacity，避免 push_back 自动分配 | reverse(len)，预留空间，不可初始化，不可赋值      |
| shrink_to_fit | 请求降低存储使得 capacity 和 size 匹配     |                                   |

## Elements access

| Name        | Role                      | Notice |
| ----------- | ------------------------- |:------:|
| operator[ ] | 迭代器访问容器元素                 |        |
| at          | 更加安全的访问元素，能判别是否越界并确定程序中止  |        |
| front       | 返回第一个元素的引用                |        |
| back        | 返回最后一个元素的引用               |        |
| data`c11`   | 返回一个指向数组中第一个元素的指针（向量内部使用） |        |

## Modifiers

| Name         | Role                              | Notice                                                                        |
|:------------ | --------------------------------- |:----------------------------------------------------------------------------- |
| assign       | 替换旧元素为向量元素分配新值，也可修改向量大小           | assign(begin, end)<br/>assign(n, elem)                                        |
| push_bach    | 向矢量的末尾添加元素                        |                                                                               |
| pop_back     | 删除矢量最后一个元素                        |                                                                               |
| insert       | 指定位置插入一个或多个元素（构造并复制）              | insert(iter, elem)<br/>insert(iter,count,elem)<br/>insert(pos, _first, _last) |
| erase        | 删除元素                              | erase(pos)<br/>erase(_first, _last)                                           |
| swap         | 交换容器，内存释放（拷贝构造，capacity == size ） |                                                                               |
| clear        | 清除容器                              |                                                                               |
| emplace      | 指定位置插入一个元素（就地构造，转发实现）             |                                                                               |
| emplace_back | 矢量末尾插入一个元素（就地构造，转发实现）             |                                                                               |

## Allocator

| Name          | Role       | Notice |
| ------------- | ---------- |:------:|
| get_allocator | 返回矢量的内存分配器 |        |

## Non-member Notice overloads

| Name                 | Role          | Notice |
| -------------------- | ------------- |:------:|
| relational operators | 关系型函数（ =、>、<） |        |
| swap                 | 交换容器的内容       |        |

## Template specializations

| Name         | Role                                                 | Notice |
| ------------ | ---------------------------------------------------- |:------:|
| vector<bool> | 特殊的标准容器，1 bit 存储（byte 可实现动态位集），返回的不是 bool &，而是一个代理对象 |        |

# Forward_list

- #include<forward_list>    using namespace std;

- 单链表：数据的存储位置是分散的、随机的，整个链表的数据的线性关系通过指针来维持；

- 擅长在序列的任何位置进行元素的插入或删除操作，访问元素效率低；

- 只能从前向后遍历，只提供前向迭代器；

- 存储相同个数的同类型元素，耗用的内存空间更少，空间利用率高；。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-12-15-35-41-image.png" title="" alt="" data-align="center">

## Member functions

| Name          | Roel                                   | Notice                                         |
| ------------- | -------------------------------------- | ---------------------------------------------- |
| cbefore_begin | 和 before_begin 功能相同，在原有基础上增加了 const 属性 | before_begin( ) : 返回前向迭代器，指向第一个元素之前的位置         |
| cbegin        | 和 begin 功能相同，在原有基础上增加了 const 属性        | begin( ) : 返回前向迭代器，指向第一个元素的位置                  |
| cend          | 和 end 功能相同，在原有基础上增加了 const 属性          | end( ) : 返回前向迭代器，指向最后一个元素之后的位置                 |
| assign        | 用新元素替换容器中的原有内容                         |                                                |
| emplace_front | 在容器头部生成一个元素，和 push_front 相同，效率更高       | push_front( ) : 容器头部插入一个元素                     |
| emplace_after | 和 insert_after 功能相同，但效率更高              | insert_after( ) : 指定位置之后插入一个新元素，并返回一个指向新元素的迭代器 |
| erase_after   | 删除容器中某个指定位置或区域内的所有元素                   |                                                |
| remove_if     | 删除容器中满足条件的元素                           | remove(val) : 删除容器中所有等于 val 的元素                |
| merge         | 合并两个事先已排好序的容器，且合并之后的容器依然是有序的           |                                                |
| advance       | 容器前向单步移动                               | 除了使用 ++ 运算符，还可以advance(it, 2);                 |

# List

- #include<list>; using namespace std;

- 双向链表 ：元素存储在连续的列表容器中，封装：链表；

- 动态存储分配，由一系列结点（数据域、指针域）构成；

- 支持对任意位置的插入、删除；

- 支持 ++ / -- 双向访问，不支持 += / -= 随机访问；

- 按顺序访问，遍历速度没有数组快，占用空间比数组大；

- 内存空间是不连续的，通过指针来访问访问数据，

- [List 接口函数介绍](https://vimsky.com/examples/usage/post-cpp-list-size-function-01.html)。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-05-11-11-26-image.png" title="" alt="" data-align="center">

## Elements access

| Name  | Role        | Notice |
| ----- | ----------- |:------:|
| front | 返回第一个元素的引用  |        |
| back  | 返回最后一个元素的引用 |        |

## Modifiers

| Name                | Role                               | Notice                               |
| ------------------- | ---------------------------------- |:------------------------------------ |
| assign              | 赋值，替换旧元素为列表容器添加新元素                 | assign(beg, end)<br/>assign(n, elem) |
| emplace_front `c11` | 列表开头插入新元素，容器大小增加 1（就地构造）           |                                      |
| push_front          | 列表开头插入新元素，容器大小增加 1                 |                                      |
| pop_front           | 列表开头删除元素，容器大小减少 1                  |                                      |
| emplace_back `c11`  | 列表末尾插入新元素，容器大小增加 1（就地构造）           |                                      |
| push_back           | 列表末尾插入新元素，容器大小增加 1                 |                                      |
| pop_back            | 列表末尾删除元素，容器大小减少 1                  |                                      |
| emplace `c11`       | 指定位置插入新元素来扩展列表，返回一个指向新插入元素的随机访问迭代器 |                                      |
| insert              | 指定位置插入一个或多个元素                      | insert(pos,beg,end)                  |
| erase               | 指定位置删除一个元素，返回指向下一参数的随机访问迭代器        | rease(beg, end)<br/>erase(pos)       |
| swap                | 与另一相同类型的列表交换列表的内容                  |                                      |
| resize              | 重新设置列表大小（尺寸更大则补充，int 默认 0）         | resize(num, elem)                    |
| clear               | 删除列表所有元素，使其大小为 0                   |                                      |

## Operations

| Name      | Role                                       | Notice                |
| --------- | ------------------------------------------ |:---------------------:|
| splice    | 部分或全部删除源列表的内容，并插入到目的 list                  |                       |
| remove    | 从列表中删除与作为函数参数给出的值相对应的所有值                   | remove(elem)          |
| remove_if | 从列表中删除与谓词或条件相对应的所有值                        |                       |
| unique    | 从列表中删除所有连续出现的重复元素                          |                       |
| merge     | 以递增的顺序合并两个已排序的列表。compare : 比较器，实现内部比较合并列表。 | merge(list,  compare) |
| sort      | 按递增顺序排列给定列表的元素（仅在列表中移动）                    |                       |
| reverse   | 反转列表中元素的顺序                                 |                       |

## Observes

| Name          | Role        | Notice  |
| ------------- | ----------- |:-------:|
| get_allocator | 返回与列表关联的分配器 | 函数不抛出异常 |

# Deque

- #include<deque>    using namesace std;

- 双端队列容器 ：<mark></mark>双向开口的连续线性空间；

- 擅长尾部和头部添加或删除元素：常数阶；

- 存储元素并不能保证所有元素都存储到连续的内存空间中；

- deque 是动态的以分段连续空间组合而成（没有提供所谓空间保留 server 功能）；

- 是分段连续内存空间，有中央控制，维持整体连续的假象；

- deque 最大的工作就是维护分段连续的内存空间的整体性的假象，并提供随机存取的接口，避开了重新配置空间、复制、释放的轮回，代价就是复杂的I迭代器架构；

- [deque 接口函数介绍]([C++ deque get_allocator用法及代码示例 - 纯净天空](https://vimsky.com/examples/usage/deque-get_allocator-in-c-stl.html#:~:text=C%2B%2B%20deque%20get_allocator%E7%94%A8%E6%B3%95%E5%8F%8A%E4%BB%A3%E7%A0%81%E7%A4%BA%E4%BE%8B%20%E5%8F%8C%E7%AB%AF%E9%98%9F%E5%88%97,%3A%3Aget_allocator%20%28%29%20%E6%98%AFC%2B%2B%20STL%E4%B8%AD%E7%9A%84%E5%86%85%E7%BD%AE%E5%87%BD%E6%95%B0%EF%BC%8C%E7%94%A8%E4%BA%8E%E8%8E%B7%E5%8F%96%E5%AE%B9%E5%99%A8%E5%8F%8C%E7%AB%AF%E9%98%9F%E5%88%97%E7%9A%84%E5%88%86%E9%85%8D%E5%99%A8%E3%80%82))。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-06-10-37-20-image.png" title="" alt="" data-align="center">

## Capacity

| Name          | Role                                  | Notice |
| ------------- | ------------------------------------- |:------:|
| size          | 返回矢量实际存储元素数目（实际数据量）                   |        |
| max_size      | 返回矢量所能存储的最大的元素数目                      |        |
| resize        | 更改矢量实际存储，指定长度若变长则默认填充新位置，变短则删除超出长度的元素 |        |
| empty         | 判断矢量存储是否为空                            |        |
| shrink_to_fit | 请求降低存储使得 capacity 和 size 匹配           |        |

## Element access

| Name        | Role      | Notice               |
| ----------- | --------- | -------------------- |
| operator[ ] | 下标访问元素    | 越界不抛出异常，直接报错         |
| at          | 返回索引所指的元素 | 如果越界则抛出 out_of_range |
| front       | 返回第一个元素   |                      |
| back        | 返回最后一个元素  |                      |

## Modifiers

| Name               | Role                   | Notice                                                                                          |
| ------------------ | ---------------------- | ----------------------------------------------------------------------------------------------- |
| assign             | 将数据赋值给本身               | 拷贝 n 个 elem ：assign(n, elem)<br/>拷贝区间数据：assign(beg, end)                                        |
| push_back          | 在容器尾部添加一个元素            |                                                                                                 |
| push_front         | 在容器头部添加一个元素            |                                                                                                 |
| pop_back           | 删除容器最后一个元素             |                                                                                                 |
| pop_front          | 删除容器第一个元素              |                                                                                                 |
| insert             | 指定位置插入元素的拷贝            | 插入一个并返回位置：insert(pos, elem)<br/>插入 n 个：insert(pos, n, elem)<br/>插入区间 [ ) ：insert(pos, beg, end) |
| erase              | 删除指定数据，返回下一个数据的位置      | 删除区间 [ ) ：erase(beg, end)<br/>删除指定位置：erase(pos)                                                 |
| swap               | 交换另一相同类型的双端队列的内容       |                                                                                                 |
| clear              | 移除容器所有数据               |                                                                                                 |
| emplace`c11`       | 指定位置直接构造生成一个元素         |                                                                                                 |
| emplace_front`c11` | 容器头部生成一个元素，直接构造，减少复制移动 |                                                                                                 |
| emplace_back`c11`  | 容器尾部生成一个元素，直接构造，减少复制移动 |                                                                                                 |

## Allocator

| Name          | Role         | Notice |
| ------------- | ------------ | ------ |
| get_allocator | 获取容器双端队列的分配器 |        |

## Notice

- 相比 vector ，增加了头部添加和删除的成员函数，同时删除了 capacity( )、reverse( )、data( ) 成员函数；

- swap(deque1, deque2) ：交换存储相同类型的元素，功能相同，语法不同；

# Stack

- #include<stack>    using namespace std;

- 栈：LIFO，先进后出；

- 不允许遍历，仅仅一个出口，只有栈顶元素可被访问到。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-05-11-20-16-image.png" title="" alt="" data-align="center">

## Member functions

| Name          | Role        | Notice                                            |
| ------------- | ----------- | ------------------------------------------------- |
| （constructor） | 基本构造函数      | 指定元素数量以及默认值：<br/>std::list<int> lis(10, 5);       |
| empty         | 判断是否为空      |                                                   |
| size          | 返回当前的元素个数   |                                                   |
| top           | 返回最后一个安插的元素 | reference & top( );<br/>const_reference & top( ); |
| push          | 安插一个元素      |                                                   |
| emplace       | 构造与安插元素     |                                                   |
| pop           | 移除容器内的下一个元素 |                                                   |
| swap          | 交换两个容器的内容   |                                                   |

# Queue

- #include<queue>    using namespace std;

- 队列：FIFO，先进先出；

- 仅仅两个出口，不允许遍历，只能访问容器适配器的第一个和最后一个元素；

- 只能在容器末尾添加元素，只能在容器头部移除元素；

- 和 stack 一样，queue 没有迭代器，不可直接访问所有元素，访问元素唯一的方式就是遍历容器，并移除访问过的每一个元素；

- [queue 的实际使用](http://c.biancheng.net/view/479.html)。

<img src="file:///D:/Download/MarkText/workspace/image/2022-09-07-16-39-50-image.png" title="" alt="" data-align="center">

## Member Functions

| Name    | Role                                             | Notice                 |
| ------- | ------------------------------------------------ | ---------------------- |
| queue   | 拷贝构造                                             | queue(const queue & q) |
| front   | 返回队首元素                                           |                        |
| back    | 返回队尾元素                                           |                        |
| push    | 队尾添加一个元素副本                                       | 入队 O(1)                |
| pop     | 删除第一个元素                                          | 出队 O(1)                |
| size    | 返回队列中元素个数                                        | 返回值类型 Unsigned int     |
| emplace | 用传给 emplace() 的参数调用 T 的构造<br/>函数，在 queue 的尾部生成对象 |                        |

# set / multiset

- #include<set>    using namespace std;

- 集合：属于关联式容器，每插入一次数据都会根据特定排序准则自动排序；

- 底层用红黑树（封装：平衡检索二叉树），set / multiset 属于关联式容器；

- set 元素唯一，插入数据的同时会返回插入结果，multiset 不检测插入元素，可重复；

- 数据自动排序（从小到大）且数据唯一（只有一个键值，且键值唯一）；

- ```cpp
  // 利用仿函数改变排序规则
  class MyCompare
  {
      public:
          bool operator()(int v1, int v2)
          {
              return v1 > v2;
          }
  }
  
  set<int, MyCompare> s2; // 从大到小
  
  class Person{ ... };
  // 当自定义数据类型时，必须指定排序规则才可修改容器
  set<Person, MyCompare> s2; // 从大到小
  ```

- 不能存储无法比较大小的数据，构建 RB 树需要进行比较；

- 元素值就是其键值，其迭代器是一 const 迭代器，无法由此进行改变元素值；

- [C++ set rend()用法及代码示例 - 纯净天空](https://vimsky.com/examples/usage/cpp-set-rend-function-01.html)。

| Name    | Role                         | Notice |
| ------- | ---------------------------- | ------ |
| rbegin  | 返回引用集合最后一个元素的反向迭代器           |        |
| rend    | 返回一个反向迭代器，指向容器的最后一个元素之后的元素   |        |
| cbegin  | 返回一个常量迭代器，指向集合的第一个元素         |        |
| cend    | 返回一个常量迭代器，指向集合的最后一个元素        |        |
| crbegin | 返回引用集合最后一个元素的反向常量迭代器         |        |
| crend   | 返回一个常量反向迭代器，指向容器的最后一个元素之后的元素 |        |

## Capacity

| Name     | Role             | Notice                  |
| -------- | ---------------- | ----------------------- |
| empty    | 判断是否为空           |                         |
| size     | 返回集合中元素个数        | size_type size() const; |
| max_size | 返回集合中最大所能存储的元素数量 |                         |

## Modifiers

| Name              | Role                    | Notice                                               |
| ----------------- | ----------------------- | ---------------------------------------------------- |
| insert            | 插入一个元素，类型与容器元素一致        | insert(elem)                                         |
| erase             | 删除一个或者区间的元素，将会自动缩减空间使用  | erase(pos)<br/>erase( iterator first, iterator last) |
| swap              | 交换两个集合的内容               | 可以大小不同，但一定要类型相同                                      |
| clear             | 清空集合中的元素                | 不会改变所占用文档最大内存空间                                      |
| emplace`c11`      | 向容器中插入新元素来扩展集合          | 直接构建、不复制不移动                                          |
| emplace_hint`c11` | 参数中传递一个位置做为提示，在集合中插入新元素 | 在元素当前位置插入元素之前从中进行搜索操作的提示，不能指定位置，仅在容器的属性之后插入          |

## Observers

| Name       | Role                    | Notice                                                                                                   |
| ---------- | ----------------------- | -------------------------------------------------------------------------------------------------------- |
| key_comp   | 返回比较对象的副本，设置容器使用该副本来比较键 | operator bool ( const  Key &  _Left , const Key &  _Right );    如果 _Left 在排序顺序中排在前面且不等于 _Right，则返回 true。 |
| value_comp | 返回比较对象的副本，设置容器使用该副本来比较键 | value_compare set_name.value_comp( )  第一个参数在第二个参数之前，则 true，否则 false。如果自反地返回false，则认为两个元素是等效的。            |

## Operations

| Name        | Role                                                                              | Notice                                                              |
| ----------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| find        | 查找元素，返回一个迭代器类型指针                                                                  | find(key)                                                           |
| count       | 判断某个元素是否在集合内，存在返回 1                                                               | set.count(i) != 0                                                   |
| lower_bound | 返回一个指向刚好等效于 k 的元素的迭代器，如果不存在则返回刚好大于 k 的下一个元素，如果超过了最大值，则返回等效于 s.end( ) 的迭代器指向最后一个元素 |                                                                     |
| upper_bound | 返回一个指向刚好大于 k 的下一个元素的迭代器                                                           | set.upper_bound( k )                                                |
| equal_range | 返回包含容器中所有具有等于 k 的键的元素的范围的迭代器，下界是本事，上限是指向 k 之后的下一个元素                               | 如果没有匹配，则根据内部对象返回范围长度为 0 ，两个迭代器均大于 k 的第一个元素，如果超过最大元素，则返回指向最后一个元素的迭代器 |

## Allocator

| Name          | Role     | Notice |
| ------------- | -------- | ------ |
| get_allocator | 获取集合的分配器 |        |

# map

- #include <map>    std::map<int, std::string> person;

- 所有元素都是对组，都会根据元素的 key 自动排序，仿函数可改变排序规则；

- hash 键值对：每个关键字（key）只能在容器中出现一次，第二个为映射值（value）；

- key 和 value可以是任意需要的类型，包括自定义类型；

- 容器存储的是 pair 类型（pair<const K, T>）的元素;

- 容器存储的各个键值对，key 既不能重复也不能被修改；

- 属于关联式容器，底层结构用二叉树实现；

- 根据 key 值快速查找 value 值，高效；

- [映射容器接口函数]([C++ STL map容器详解](http://c.biancheng.net/view/7173.html))。

## Capacity

| Name       | Role             | Notice                         |
| ---------- | ---------------- | ------------------------------ |
| empty      | 检查 Map 容器是否为空    | 1. 没有异抛出保证；<br/>2. 传递参数时显示错误。  |
| size       | 返回映射中存在的元素总数     |                                |
| max_size   | 返回映射中所能容纳的最大元素数量 |                                |
| value_type | 值类型              | map<int,int>::value_type(1, 1) |

## Element access

| Name        | Role              | Notice                                    |
| ----------- | ----------------- | ----------------------------------------- |
| operator[ ] | 用于引用运算符内部给定位置处的元素 | 1. 无 Key，显示未定义行为；<br/><br/>2. 否则没有异常抛出保证。 |
| at`c11`     | 用于引用映射到键值的元素      | 1. 对应键不在映射中抛异常；<br/>2. 否则具有强大的无异常抛出保证。    |

## Modifiers

| Name         | Role                        | Notice                                                                                                                                                |
| ------------ | --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| insert       | 用于在 Map 中插入具有特定键的元素         | insert( {key, element} )<br/>( position, {key, element} )<br/>( position1, position2)<br/>( pair<key, value>( k, v ) )                                |
| erase        | 指定要在 Map 中擦除的 key           | 在映射中找到关键元素，返回 1，否则 0；<br/>erase( iterator position ) : 删除指定位置；<br/>erase( position1, position2) ：左闭右开 <br/>erase( key )                               |
| swap         | 用于交换两个 Map 的内容              | 映射类型必须相同，尽管大小可能会有所不同                                                                                                                                  |
| clear        | 用于从Map容器中删除所有元素，从而使其大小保持为 0 |                                                                                                                                                       |
| emplace      | 插入新元素来扩展 map                | 元素是直接构建的（既不复制也不移动）                                                                                                                                    |
| emplace_hint | 功能和 emplace() 类似            | 1. 不仅要传入创建键值对所需数据，还需要传入一个迭代器作为第一个参数，指明插入的位置（新键值对键会插入到该迭代器指向的键值对的前面）；<br/>2. 返回值是一个迭代器。成功插入时，返回的迭代器指向新插入的键值对；插入失败，则表明 map 容器中存有相同键的键值对，返回的迭代器就指向这个键值对。 |

## Observers

| Name       | Role                               | Notice |
| ---------- | ---------------------------------- | ------ |
| key_comp   | 返回容器使用的比较对象的副本                     |        |
| value_comp | 返回一个函数对象，该对象比较std::map::value类型的对象 |        |

## Operators

| Name        | Role                               | Notice                                                                                                       |
| ----------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| find        | 返回一个迭代器或常量迭代器，该迭代器或常量迭代器引用键在映射中的位置 | 如果映射容器中不存在该键，则它返回引用 map.end() 的迭代器或常量迭代器。                                                                    |
| count       | 统计 key 的元素个数                       | count( key )，结果为 0 或 1                                                                                       |
| lower_bound | 返回指向容器中键的迭代器，<br/>该迭代器等效于参数中传递的 k  | 在映射容器中不存在k，则该函数返回一个迭代器，该迭代器指向刚好大于k的紧邻的下一个元素。<br/>如果在参数中传递的键超过了容器中的最大键，则迭代器返回指向映射中元素数量的键，即 key 和 element = 0。 |
| upper_bound | 返回一个迭代器，该迭代器指向刚好大于k的下一个元素          | 果在参数中传递的键超过了容器中的最大键，则迭代器返回的点将作为 key 和 element = 0 指向映射容器中的元素数。                                               |
| equal_range | 返回一个 pair 对象（包含 2 个双向迭代器）          | 其中 pair.first 和 lower_bound() 方法的返回值等价，pair.second 和 upper_bound() 方法的返回值等价                                  |

## Allocator

| Name          | Role           | Notice |
| ------------- | -------------- | ------ |
| get_allocator | 返回与 map 关联的分配器 |        |

# pair

- #include<utility> using namespace std;

- pair 属于类模板，同容器一样，用来创建“键值对”形式的元素；

- 实现是一个结构体，主要成员变量：first、second；

- 以使用 make_pair 函数生成对组 pair ；

```cpp
template pair make_pair(T1 a, T2 b) { return pair(a, b); }
// make_pair 函数生成 pair 时接受隐式的类型转换
```

- 重载了 <、<=、>、>=、==、!= 这 6 个运算符，其运算规则是：对于进行比较的 2 个 pair 对象，先比较 pair.first 元素的大小，如果相等则继续比较 pair.second 元素的大小。

## Modifiers

| Name                   | Role           | Notice                                                          |
| ---------------------- | -------------- | --------------------------------------------------------------- |
| pair                   | 构造函数           | 空：pair();<br/>pair (const first_type& a, const second_type& b); |
| pair(const pair<U,V>&) | 拷贝构造           | template< U,V> pair(const pair<U,V>& pr);                       |
| pair (pair<U,V>&&)     | 移动构造           | template< U,V> pair (pair<U,V>&& pr);                           |
| pair (U&&, V&&)        | 右值引用           | template< U,V> pair (U&& a, V&& b);                             |
| make_pair()            | 移动构造           | 返回值（一个临时对象）作为参数传递给 pair() 构造函数                                  |
| swap                   | 用于交换两个 Map 的内容 | 映射类型必须相同，尽管大小可能会有所不同                                            |
