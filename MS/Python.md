## Python

- 是一种**解释型**语言，无需在运行前进行编译；
- 是**动态类型的**，不需要声明变量的类型；
- **区分大小写**，所有代码都在**缩进**块中指定；
- 允许定义类以及组成和继承。Python没有访问作用域；
- **函数**是**一流的对象**。可以将它们分配给变量，从其他函数返回并传递给函数；
- 编写速度快，但运行它通常比编译语言要慢；但 Python 允许包含基于 C 的扩展；
- 使用领域：Web应用程序，自动化，科学建模，大数据应用程序等；经常用作“胶水”代码，辅佐其他语言和组件；

### 管理内存

1. 由 **Python 专用堆空间管理**。所有Python对象和数据结构都位于私有堆中。程序员无权访问此私有堆；
2. Python 对象的堆空间分配是**由 Python 的内存管理器完成**的。核心 API 允许访问一些工具，以便程序员进行编码；
3. Python 还具有一个**内置的垃圾收集器**，该垃圾收集器**回收所有未使用的内存**，并使其可用于堆空间；

### 模块

- **包含 Python 代码**的文件；
- 可以是**函数类或变量**；
- 是包含可执行代码的 **.py 文件**；
- 常用的**内置模块**：操作系统-**os**、系统-**sys**、数学-**math**、随机-**random**、资料时间-**data ime**、**JSON** 格式；

## 数据类型

### 类型转换

| 转化函数  | 说明                             |
| --------- | -------------------------------- |
| **int**   | 将任何数据类型转换为整数类型     |
| **float** | 将任何数据类型转换为float类型    |
| **ord**   | 将字符转换为整数                 |
| **str**   | 将整数转换为字符串               |
| **hex**   | 将整数转换为十六进制             |
| **oct**   | 将整数转换为八进制               |
| **tuple** | 转换为元组                       |
| **set**   | 转换为 set 后返回类型            |
| **list**  | 将任何数据类型转换为列表类型     |
| **dict**  | 将顺序（键，值）的元组转换为字典 |

### 数组 Array

- **只能容纳一个数据类型元素**，与列表具有相同的数据存储方式；



### 列表 list

- 可以**容纳任何数据类型元素**；
- 是**动态**的，长度可以改变。随意操作元素；
- 需要**更多的内存**；
- **不可被 hash**；

```python
# 初始化
a = list()
b = []
c = list(range(1,6)) # range 快速创建

# 内存更大
list_t = []
print("列表初始化时候大小:", list_t.__sizeof__()) # 40

# append：在列表的末尾添加一个元素
l.append("python")

# extend：使用可迭代对象中的所有元素来扩展列表
t = ["java"]
l.extend(t)

# insert: 在给定的位置插入一个元素
l.insert(1,"go") # ['python', 'go', 'java']

# remove(x)：从列表中删除值为x的第一项，若没有可 remove 则抛出异常
l.remove("java")

# pop: 删除列表中给定位置的元素并返回，若没有则删除列表最后一个元素
l.pop() # 'go'

# del: Python 中的关键字，用来执行删除操作，可删除整个列表、也可删除元素
l = ["python", "java", "go", "js"]
del l[0:1] # ['java', 'go', 'js']
del l[0] # ['go', 'js']

# clear(): 移除列表中的所有元素。等价于 del a[:]

# 修改元素
l = ["python", "go", "java"]
l[0] = "PYTHON" # ['PYTHON', 'go', 'java']
l[0:2] = "PYTHON", "GO" # ['PYTHON', 'GO', 'java']

# index(x) ：查找某个元素的位置（索引），如果不存在，则 ValueError 错误
l.index("PYTHON") # 0

# count() :用来统计某个元素在列表中出现的次数
# sort：对列表中的元素进行排序
# reverse: 反转元素

# 反转数据或序列的元素
import array as arr
My_Array=arr.array('i',[1,2,3,4,5])
My_Array[::-1] # array（'i'，[5，4，3，2，1]）


# copy: 返回列表的一个浅拷贝，等价于 a[:]
a = l.copy()
```

### 元组 tuple

- 属于**静态**、大小固定的；
- 不可对元素进行增删改；
- 元组需要的**内存更少**；
- **可被 hash**，初始化速度快；

```python
# 更少的内存
tuple_t = ()
print("元组初始化时候大小:", tuple_t.__sizeof__()) # 24

# 将字符串转换成元组
tup1 = tuple("hello") # ('h', 'e', 'l', 'l', 'o')
 
# 将列表转换成元组
list1 = ['Python', 'Java', 'C++', 'JavaScript']
tup2 = tuple(list1) # ('Python', 'Java', 'C++', 'JavaScript')
 
# 将字典转换成元组
dict1 = {'a': 100, 'b': 42, 'c': 9}
tup3 = tuple(dict1) # ('a', 'b', 'c')
 
# 将区间转换成元组
range1 = range(1, 6)
tup4 = tuple(range1)

# 访问元素
a = (1, 2, 3, 4, 5)
print(a[0]) # 下标
print(a[0:4:2]) # 切片：a[start : end : step] ## (1, 3)

# 删除 
del a
```





## 变量与函数

### 全局 / 局部变量

- 在函数外部或**全局**空间中声明的变量，**任何函数都可以访问**；
- 在函数**内部**声明的任何变量，存在于**局部空间**；

### 函数

- 定义：使用 **def** 关键字；

```python
def Newfunc():
	print("A")
Newfunc(); #calling the function
```

#### init

- **构造函数**，当创建类对象或实例时，将自动调用此方法进行内存分配；
- **所有类都具有** init 方法；

```python
class Employee:
def __init__(self, name, age, salary):
self.name = name
self.age = age
self.salary = 20000
E1 = Employee("XYZ", 23, 20000)
# E1 is the instance of class Employee.
#__init__ allocates memory for E1. 
print(E1.name)
print(E1.age)
print(E1.salary)
```

#### Self

- 类的**实例或对象**；
- 包含为**第一个参数**，有助于区分具有局部变量的类的方法和属性；
- self 变量引用新创建的对象，**类似 this**；

#### lambda

- 匿名函数；
- 可以具有任意数量的参数，但是只能有一个语句；

```python
a = lambda x,y : x+y
print(a(5, 6))
```

