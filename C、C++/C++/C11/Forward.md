## std::Forward

**完美转化**，保持原来的 `值` 属性不变，如果原来的值是左 / 右值，经过 std::forward 处理后仍为左 / 右值（如果一个数可以获取它的地址，那就是左值，否则为右值）。

> 当处理右值引用类型的时候，它的参数本身是个左值，右值代表了这个对象可以进行 move 操作。

### arguments 与 parameters

arguments 是指函数定义中的参数，parameters 是指函数调用时的实际参数。

```cpp
void someFunc(Widget w); //parameter w is passed by value
Widget wid;
someFunc(wid); // copy construction
someFunc(std::move(wid)); // move construction
```

1. 左值的 copy 是通过 copy constructor 进行的；

2. 右值的 copy 是通过 move constructor 完成的；

3. 当使用另一个对象初始化一个对象时，新的对象是初始化对象的 copy。

### 右值引用（rvalue references）和全局引用（universal references）

#### T &&

1. 代表的是右值引用。就是它绑定到一个右值上，代表对象的移动来源；

2. 代表的可能是右值引用也可能是左值引用，叫做全局引用。

#### 全局引用

1、函数模板：

```cpp
template<typename T> void f(T && param) // universal references
```

2、auto 的使用：

```cpp
auto && var2 = var1;
```

#### 右值引用

```cpp
void f(Widget && param) // no type param is an rvalue reference
Widget && var1 = Widget(); // rvalue reference
```

1、虽然 T 需要类型推导，但是 param 是 vector ，而不是 T&&

```cpp
template<typename T>
void f(std::vector<T> && param) // param is an rvalue reference
```

2、与 const 关键字：

```cpp
template <typename T>
void f(const T && param);  //param is an rvalue reference
```

如果函数返回值，返回对象绑定到右值引用或者全局引用，可以使用 std::move 或者 std::forward 返回这个引用：

```cpp
Matrix operator+(Matrix&& lhs,const Matrix&rhs)
{
  lhs += rhs;
  return std::move(lhs); //move lhs into return value
}

Matrix operator+(Matrix&& lhs,const Matrix&rhs)
{
  lhs += rhs;
  return lhs;   //copy lhs into return value
}
```

如果 Matrix 支持 move 操作，则第一种情况更好，如果 Matrix 不支持，由于右值会被 Matrix 的拷贝构造函数复制，如果后面 Matrix 支持了 move 操作则后面会自动转为第一种获益情况。

> move 是无条件的转换为右值；
> 
> forward 是有条件的转换。

- 如果希望从对象 moving 则将对象设置为 const；

### 实现原理

```cpp
template <typename T>
T&& forward(typename std::remove_reference<T>::type& param)
{
    return static_cast<T&&>(param);
}

template <typename T>
T&& forward(typename std::remove_reference<T>::type&& param)
{
    return static_cast<T&&>(param);
}
```

forward 实现了两个模板函数，一个接收左值，另一个接收右值。在上面有代码中：

```cpp
typename std::remove_reference<T>::type
```

获得去掉引用后的参数类型。第一个是左值引用模板函数，第二个是右值引用模板函数。
