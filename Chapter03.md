# Chapter 3 字符串、向量和数组

## 3.1 命名空间和using声明

**using声明(using declaration):**
```cpp
// using namespace::name;
using std::string; // every name must be declared via this method

// more convenient method
using namespace std;
```

*头文件不应该包含using声明！*

## 3.2 标准库类型string

**string类型：**string类型可以表示可变长的字符序列，使用string类型前必须包含string头文件，其定义在命名空间std中。

### 3.2.1 定义和初始化string对象

**Some Examples：**

```cpp
string s1; // 默认初始化，s1是一个空字符串
string s2(s1); // 拷贝初始化，s2是s1的一个副本
string s3 = "hiya"; // s3是该字符串字面值的副本
string s4(n, 'c'); // s4中保存了n个c组成的字符串
```

#### 直接初始化和拷贝初始化

1. **拷贝初始化(copy initialization):** 使用赋值号来初始化一个变量，编译器把赋值号右边的初始值拷贝到新创建的对象中去；
2. **直接初始化(direct initialization):** 不适用赋值号。

* 当初始值只有一个时，使用直接初始化和赋值初始化都行。
* 对于用多个初始值进行初始化的时候，虽然可以强行使用拷贝初始化，但该方法会创造一个临时对象来进行直接初始化，再使用该对象去拷贝初始化对象，会造成资源的浪费：
```cpp
string s8 = string(10, 'c');
// this expression is equivalent to below expressions
string temp(10, 'c');
string s8 = temp;
```

### 3.2.2 string对象上的操作

```cpp
os << s; // 将s写入到输出流os中去并返回os
is >> s; // 从输入流is读入字符并写入s，字符串以空白分割，返回is
getline(is, s); // 从is读入一行字符并赋值给s返回is
s.empty(); // 如果字符串为空，则返回true
s.size(); // 返回字符串的长度
s[n]; // 返回字符串的第n个字符的引用（从0开始）
s1 + s2; // 将两个字符串连接起来
s1 = s2; // 将s2中的字符串赋值给s1，s1是s2的一个副本
s1 == s2; // 如果两个字符串完全相等则返回true
s1 != s2; // 如果两字符串内容不完全相等则返回true
<, <=, >, >= // 根据字典顺序来比较字符串，大小写敏感
```

#### 读写string对象

**在执行读取操作的时候，string对象会自动跳过开头的空白（包括空格、制表、换行等符号）并从第一个真正的字符开始一直读到下一处空白为止。所以，这个操作可以实现自动分词。**

**读取操作可以写入多个string对象：** 因为string类型的读入操作总是会返回输入流，所以可以作为下一个对象的运算对象。

```cpp
string s1, s2, s3;
// input: "Hello CPP World!"
std::cin >> s1 >> s2 >> s3;
// s1: "Hello", s2: "CPP", s3: "World!"
std::cout << s1 << s2 << s3; // output: "HelloCPPWorld!"
```

#### 读取位置数量的string对象

```cpp
while(std::cin >> word) std::cout << world << std::endl;
```

一般情况下，当输入的字符串为空字符串的时候while循环就会停止。
*在win中，你应该使用`Ctrl+Z`来终止输入操作。*

#### 使用getline来读取一整行字符串

当你想要保留输入的空白时，就应该使用getline函数来读取一整行的字符串：

```cpp
while(getline(cin, word)) cout << word << endl;
```

#### string::size_type 类型

string::size_type是定义在string中的类类型，用来指示string大小的类型。

本质上，string::size_type是一个无符号整型，该数值能够存放任何string的大小。

可以使用auto来推断该类型：

```cpp
auto len = line.size();
```

### 3.2.3 处理string对象中的字符

**cctype头文件中的函数：**

function|description
---|---
`isalnum(c)` | c是字母或数字时为真
`isalpha(c)` | c是字母时为真
`iscntrl(c)` | c为控制字符时为真
`isdigit(c)` | c为数字时为真 
`isgraph(c)` | c不是空格但是可以打印为真
`islower(c)` | c是小写字母为真
`isupper(c)` | c是大写字母为真
`isprint(c)` | c是可打印字符为真
`ispunct(c)` | c是标点符号为真
`isspace(c)` | c是空白为真
`isxdigit(c)` | c是十六进制数字为真
`tolower(c)` | 返回c的小写值，非引用
`toupper(c)` | 返回c的大写值，非引用


#### 利用增强for循环来处理每个字符

**增强for循环：**

```cpp
for (declaration : expression)
    statement
```

**Example:输出string对象的每个字符**
```cpp
string str("some string");
for (auto c : str)
    cout << c << endl;
```

#### 使用增强for循环来改变字符串中的字符

如果想要改变stirng对象中的字符的值，必须把循环变量定义成引用类型：
```cpp
for(auto &c : str)
    c = toupper(c);
```

## 3.3 标准库类型vector

```cpp
# include <vector>
using std::vector;
```

**实例化(instantiation):** 编译器根据模板创建类或者函数的过程，当使用模板时，需要指出编译器应该把类或者函数实例化成何种类型：`vector<int> vi;`

