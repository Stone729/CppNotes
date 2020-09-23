# 第二章 变量和基本类型

## 2.3 复合类型

- **复合类型（compound type）**是指基于其他类型定义的类型
- 一条声明语句由一个**基本数据类型（base type）**和紧随其后的一个**声明符（declarator）**列表组成。

### 2.3.1 引用

- **引用（reference）**为对象起了另外一个名字，引用类型**引用（refer to）**另外一种类型。

```cpp
int ival = 1024;
int &refval = ival; // refval refer to ival
int &refVal2; // reference must be initialized
```

- **引用即别名：**引用并非对象，相反的，它只是为一个已经存在的对象所起的另外一个名字。

### 2.3.2 指针

- **指针（pointer）**是“指向（point to）”另外一种类型的复合类型。
- 指针与引用：
    - 指针本身是一种对象；
    - 指针无需在定义时赋初值。

* 定义指针：`int *ip`，定义指针时每个变量名之前都需要添加`*`符号来指示该变量为指针。
* 获取对象地址：指针存放的是某个对象的地址。获取对象的地址时，需要使用**取地址符**`&`来获取对象的地址。
* 引用不是对象，所以无法获取引用的地址，也无法定义指向引用的指针。但是可以定义指向引用所引用对象的指针。
* 利用指针访问对象：使用**解引用符**`*`来访问所指向的对象。注意此处的解引用符并不是定义指针时的`*`。
* 空指针：最好使用字面值`nullptr`来赋值空指针（C++11）
* 指针的逻辑运算(`==`、`!=`)：判断两个指针是否指向同一个对象，既两个指针所保存的地址是否相同。

### 2.3.3 理解复合类型的声明

* 理解变量的类型的方法是从右向左阅读其定义，离变量名最近的符号对变量有最直接的影响：

  ```cpp
  int i = 42;
  int *p;
  int *&r = p; // &r是一个引用，它所引用的对象的类型是int *
  ```




## 2.4 const限定符

* 使用`const`限定符对变量进行限定表示其保存的值不能被改变（常量）
* 因为const对象一旦创建后其值就不能被改变，所以const对象必须被初始化。
* 如果利用一个对象去初始化另一个对象，则它们是不是const都无关紧要。

### 2.4.1 const的引用

* **对常量的引用（reference to const）**：把引用绑定在const对象上

```cpp
const int ci = 1024;
const int &r1 = ci;
int &r2 = ci; // wrong! const int and in are not the same type
r1 = 4; // wrong! the value cannot be changed via the reference to const
```

* 引用的类型与其所引用的对象的类型不一致（情况之一）：在初始化常量引用时允许使用任意值表达式作为初始值，只要该表达式可以转化为引用的类型即可。例如，允许为一个常量引用绑定一个非常量的对象、字面值，甚至一个表达式：

```cpp
int i = 42;
const int &r1 = i; // correct! i can be changed via name i, but cannot via the r1
const int &r2 = 42; // correct! reference to const can refer to a literature value
const int &r3 = r1 * 2;
```

* 当一个常量引用绑定另外一种类型上时，系统会创建一个临时的变量（类型同引用）来保存该值（隐式类型转换），然后再使用常量引用来绑定该临时变量：

```cpp
double dval = 3.14;
const int &ri = dval;

// equivalent to:
double dval = 3.14;
const int temp = dval;
const int &ri = temp;
```

### 2.4.2 指针和const

* 类似常量引用，我们也可以定义**指向常量的指针（pointer to const）**。无法通过其改变其指向对象的值。

```cpp
const double pi = 3.14;
double *ptr = &pi; // wrong! a common pointer cannot point to a const
const double *cptr = &pi; // correct！
double dval = 3.14;
const double *cpdval = &dval; // correct! a pointer to const can point to a non-cosnt variable, but value cannot be changed via this pointer
```

##### const 指针

* 由于指针本身是个对象，所以指针本身也可以限定为const的，既**常量指针（const pointer）**。其保存的地址无法被改变，既其无法改变所指的变量，但可以修改指向变量的值：

```cpp
int errNumb = 0;
int *const curErr = &errNumb; // * & *const, the type before the * is the type of variable
*curErr = 1; // correct! the variable can be changed via zhe const pointer
```

### 2.4.3 顶层const

* **顶层const (top-level const)** 表示指针本身是个常量
* **底层const (low-level const)** 表示指针所指的对象是个常量
* 指针的特殊之处：指针类型既可以是顶层const也可以底层const
* 引用只能是一个底层const
* 普通变量只能是一个顶层const
* 在执行对象的拷贝工作时，顶层const不会有任何影响
* 当执行拷贝工作时，拷入和拷出的对象必须具有相同的底层const资格，或者两个对象的数据类型必须能够相互转换。非常量可以转换成常量，反之则不行。

### 2.4.4 constexpr和常量表达式

* **常量表达式(const expression)**是指不会改变并且在编译过程中就能得到计算结果的表达式。字面值属于常量表达式，用常量表达式初始化的const对象也是常量表达式
```cpp
const int max_files = 20; // max_files 是常量表达式
const int limit = max_file + 1; // limit 是常量表达式
int staff_size = 27; // staff_size 不是常量表达式
const int sz = get_size(); // sz 不是常量表达式
```

#### constexpr 变量
* **Why needed?** 在一个复杂的系统中很难判断一个初始值到底是不是常量表达式，例如在使用库函数的时候并不能知道其是否是const的。虽然可以直接将初始值赋值给一个const变量，但是在实际使用中这样是很不方便的。
* **C++11**中，可以将变量声明为**constexpr**类型以便由编译器来验证变量值是否是一个常量表达式，constexpr变量必须由常量表达式初始化，如果赋值给constexpr变量的不是常量表达式，编译器就会报错:
```cpp
# include <iostream>

int getone() {return 1;}

int main() {
  const int ci = getone(); // correct a top-level const can be initialized by a non-const expression
  constexpr int i = getone(); // errror! a constexpr variable must be initialized by a const expression
  constexpr int one = 1; // right! literal type can be used to initialize a constexpr variable

  return 0;
}
```

**Best Prictices:** 如果你认为一个变量一定是一个常量表达式，那就把它声明成constexpr类型

#### 字面值类型（literal type）

**字面值类型**： 常量表达式的值在编译时就要得到计算，因此对声明constexpr时用到的类型必须有所限制。因此这些类型一般比较简单，值也显而易见、容易得到，就把它们成为字面值。

#### 指针和constexpr

* 如果constexpr声明中如果定义了一个指针，限定符constexpr仅对指针有效，与指针所指的对象无关：顶层const

```cpp
const int *p = nullptr; // p是一个指向指向整型常量的指针，底层cosnt
constexpr int *q = nullptr; // q是一个指向整型的常量指针，顶层const
```

---

## 2.5 处理类型

### 2.5.1 类型别名

**类型别名(type alias):** 是某种类型的别名。

**定义类型别名：**

```cpp
// method 1
typedef double wages;
typedef wages base, *p; // base: double, p: *double

// method 2: alias declaration
using SI = Sales_item;
```

#### 指针、常量和类型别名

```cpp
typedef char *pstring;
const pstring cstr = 0;
const pstring *ps;
```

### 2.5.2 auto类型说明符

* 使用auto类型可以让编译器来判断表达式所属的类型。
* 也可以使用auto声明多个变量，但是只能有一个类型。

#### 复合类型、常量和auto

* 编译器推断出来的auto类型有时候和初始值的类型不完全一样，编译器会适当的改变结果类型使其更加符合初始化规则。
* auto一般会忽略掉顶层const，同时底层const则会保留下来。

```cpp
const int ci = i, &cr = ci;
auto b = ci; // b 是一个 int
auto c = cr; // c 是一个 int
auto d = &i; // d 是一个 整形指针
auto e = &ci; // e 是一个指向整型常量的指针
```

* 如果希望推断出的auto类型是一个顶层const：

```cpp
const auto f = ci;
```

* 可以将引用的类型设为auto：

```cpp
auto &g = ci;
auto &h = 42; // error! literal type cannot initialize reference
const auto &j = 42; // correct! literal can be bonded by const reference
```

* 设置一个类型为auto的引用时，初始值中的顶层常量属性仍然保留。


### 2.5.3 decltype 类型指示符

**decltype** 选择并返回操作数的数据类型，编译器只分析表达式的数据类型，并不会实际计算表达式的值。

```cpp
decltype(f()) sum = x; // sum 的类型为f的返回类型，但f并没有被调用
```

**decltype 处理顶层const和引用的方式与auto不同：**

* 如果decltype使用的表达式是一个变量，则decltype返回该变量的类型（包括顶层const和引用）
* auto 会忽略顶层const和引用，除非显示的表示

```cpp
const int ci = 0, &cj = ci;
decltype(ci) x = 0; // x 的类型是 const int
decltype(cj) y = x; // y is a const int reference refer to x (const int &)
decltype(cj) z; // error! reference must be initialized
```

*引用从来都是作为其所引用的对象的同义词存在的，只有在decltype处是个例外*

#### decltype和引用



---

## 2.6 编写头文件

```cpp
// header.h
# ifndef HEADER
# define HEADER

// 。。。

# endif
```