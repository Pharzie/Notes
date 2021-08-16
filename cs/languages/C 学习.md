# C 学习

[TOC]

## **C** 语言的历史和简介

**C** 语言是一个古老却依然耀眼的语言，直至今日依然在系统编程领域占据统治地位。

它诞生于 20 世纪 70 年代，早起的 **C** 为了方便编译器的实现引入了不少（或许）具有争议的特性，比如数组下标从 0 开始（最早可能追溯到 **BCPL**）、数组与指针的近乎等同、`register` 关键字等。其中也有一些影响到后来出现的语言，尤其是 **C++**。可以说，从一开始，**C** 语言就在避免一些复杂的语言特性，让其编译器便于编写的同时也让 **C** 非常贴近于汇编语言。

70 年代中期，**C** 语言经典作品 *The C Programming Language* 出版并广受欢迎。我们将这本书里描述的 **C** 语言标准以其两位作者的首字母命名，即 **K&R C**。其中包含了大部分我们熟悉的 **C** 语言特性，不过与后来的 **C** 语言标准仍有区别，我们后续会介绍。

80 年代，**C** 被广泛运用，但因为缺少标准，不同的编译器实现的特性都有所差异。为了确保 **C** 不变为一个松散的语族，**ANSI** 为 **C** 制定了一个标准，并在后来被 **ISO** 采纳。这就是我们熟知的 **ANSI C**。在这个标准中，引入了一系列有趣的术语，我们可能会在不少其它语言的标准中看到这样的描述：

- **不可移植的代码（Unportable Code）**：其中可能包括 **由实现定义的（Implementation-Defined）**，即编译器设计者所决定的，在不同编译器中的不同行为，但它们都是正确的行为；**未申明的（Unspecified）**，即标准中没有规定正确的行为，比如参数的求值顺序。
- **坏代码（Bad Code）**：即 **未定义的（Undefined）**，因为没有按照正确方式编写，程序可能采取任何可能的行动。通常，在标准中可能会给出一系列 **约束条件（Constraint）**，在没有遵守其中的要求时，程序的行为就是未定义的，比如 `%` 运算符需要操作数均为整型。在违反约束条件或语法规则时，编译器会产生警告信息；但是不受约束的未定义行为并不一定会受到检查。**C++** 中的 **未定义行为（Undefined Bahavior, UB）** 是同样的概念。
- **可移植的代码（Portable Code）**：一个 **严格遵循标准（Strictly-Conforming）** 的程序不依赖于任何未申明的或未定义的特性，它可以在任何平台的任何编译器中都会有相同的输出。相比之下，一个 **遵循标准（Conforming）** 的程序只忠于特定的编译器，因为它可能依赖于该编译器中的由实现定义的或未申明的行为。

后文中，我们只会给出遵循标准的代码。

## **C** 程序的结构

一个 **C** 程序通常是由多个源文件分别经过 **编译（Compilation）**，分别形成 **编译单元（Compilation Unit）** 后，通过链接器进行 **链接（Link）** 形成目标文件，经汇编后变为可执行文件，也即 **程序（Program）**。

### **C** 文件

一个 **C** 文件通常包括一系列 **预处理指令（Preprocessor Directive）**、**变量（Variable）** 及 **函数（Function）** 的定义及声明、**结构体（Structure）** 及 **枚举（Enumeration）** 的定义及声明，以及 **类型别名（Type Alias）** 的声明。接下来让我们快速地浏览这些概念，然后在后文中详细地介绍。

预处理指令是一些以 `#` 符号开头的标签，比如 `#define` 就是预处理定义指令，这里 `#` 和 `define` 之间可以有任意的空格。比较常用的预处理指令有下面这些：

```c
// define 指令所做的就是文本替换；
#define VARLIKE_MACRO 10

```

变量是一个由下划线、字母和数字组成的标签（我们通常称为 **标识符（Identifier）**），编译器会根据其定义让它在运行时绑定一段内存空间；程序员使用变量时，就等同于在操作其绑定的内存。变量的定义由变量 **类型（Type）** 、标识符，以及一些限定符组成，举例如下：

```c
// 定义了 int 类型的变量 i，这里 i 就是我们说的标识符。最后需要加上一个分号表明语句的末尾
int i;
// 同时定义两个同类型（double）的变量，它们之间用逗号分隔
double _d123, _d124;
// * 是指针修饰符，这是一个指针类型
char* ptr;
// static 是静态变量修饰符，这是一个静态变量
static char ch;
// [] 是数组修饰符，这是一个数组类型
int arr[5];
// (*)() 是函数指针修饰符，这是一个函数指针类型
void (*fptr)(int i);
```

函数也是一个标识符，它和一个带有参数、返回值的子程序绑定，并可以被一些限定符修饰。

```c
// 定义了 int(int, double) 类型的函数 foo，其中 (int, double) 是其参数类型，int 是其返回类型
int foo(int a, double b) {		// 子程序（函数体）的部分通过大括号包围
	// 省略函数体
}
// 如果没有返回值，那么返回类型就是 void
void bar(char* s) {
 	// 省略函数体
}
// static 是函数修饰符，这是一个静态函数；没有参数的函数可以用 void 占位，或直接省略也可
static double _abc(void) {
 	// 省略函数体  
}
```

结构体是关联一个类型模版的标识符，可以用它构造一系列变量组成的结构。

```c
// 定义了 struct IntDouble 结构，其中包含两个“变量” i 和 d
struct IntDouble {
  	int i;
    double d;
};	// 需要注意结构体的结尾处需要添加分号
// 结构体定义语句处可以直接定义变量
struct TwoDoubles {
 	double d1, d2;
} td1, td2;
// 可以定义匿名结构体，然后利用前面所述的性质定义变量
struct {
 	int* ptr;   
} ptr_wrapper;
```

枚举也是一个标识符，它为一个可数的集合声明一个类型，其中给出了这个集合中的每个元素（作为标识符给出）：

```c
// 定义了 enum Weekday 枚举类型，其有效的值有 Monday 等（其等效整型值为 0, 1, ..., 6） 
enum Weekday { Monday, Tuesday, WednesDay, Thursday, Friday, Saturday, Sunday };
enum SomeNumbers {
  	Zero,		// 枚举的第一个值默认为 0
    One,		// 没有给指定值的默认为上一个值 +1
    Three = 3,	// 可以为枚举中的元素设指定值
    Four,
    NegOne = -1,	// 枚举中元素的大小并没有规定
    AnotherZero		// 枚举中元素的值可能会重复
};
// 匿名枚举类型是合法的，也可以直接在枚举定义语句处直接定义变量
enum {
 	SomeVal, OtherVal   
} e1, e2;
```

最后让我们来看类型别名。`typedef` 关键字可以将一个类型声明为一个标识符；之后所有该标识符出现的地方都会理解为该声明。

```c
// 将内置的 int 类型声明为 MyInt
typedef int MyInt;
// 将内置的 int[10] 数组类型声明为 MyArr
typedef int MyArr[10];
// 将函数指针类型 void (*)(int, double) 声明为 MyFunc
typedef void (*MyFunc)(int i, double d);
// 将 struct MyStruct 类型声明为 MyStruct
typedef struct MyStruct MyStruct;
```

### 定义和声明

首先我们需要达成共识的是，一个程序在编译时会从第一行扫描到最后一行，且仅扫描一遍。因此一个标识符被使用之前，必须经过定义或声明。**C** 中区分了定义和声明两个概念；前者提供了一个标识符的实际定义，而后者仅声明一个标识符的存在。在编译时，声明过的标识符均可以随意使用，但是如果链接时被使用的变量找不到定义，会产生链接错误。

变量的声明是使用 `extern` 关键字，并给出变量的类型和标签：

```c
// 声明了一个变量，这个变量可能定义在任何地方，甚至其它文件中
extern int i;
int main() {
    int j;
    j = i;			// 可以使用声明过的变量，但如果 i 没有被定义，会在链接时报错
    printf("%d". j);
    return 0;
}
```

函数的声明也可以使用 `extern` 关键字，并不给出函数体，而用分号结束声明语句：

```c
// 声明了一个函数，这个函数可能定义在任何地方，甚至在其它文件中
extern void foo(int, char);		// 参数列表中的参数名称可以被省略，extern 关键字也可以被省略
int main() {
    foo(10, 'c');		// 可以调用声明过的函数，但如果 foo 没有被定义，会在链接时报错
 	return 0;   
}
// 真正定义了 foo 函数
void foo(int ct, char c) {
    while (ct--) {
        printf("%c", c);
    }
}
```

结构体的声明则只需要给出一个标签，但直到给出该类型的定义之前，这个类型都是 **不完整类型（Incomplete Type）**：

```c
// 声明了结构体 MyStruct，现在可以将其作为结构体的标识符使用了，但是目前它还是一个不完整类型
struct MyStruct;
// 声明一些帮助函数
void set_data(struct MySturct* ms, int i);
int get_data(struct MyStruct* ms);

int main() {
 	struct MyStruct ms;		// 错误！不能定义一个不完整类型的变量
    struct MyStruct* ms;	// 不完整类型的指针是一个完整类型
    set_data(ms, 10);		// 帮助函数被声明了，因此可以使用
    printf("%d", get_data(ms));
}
// 定义了结构体 MyStruct，其中有一个成员 data。之后就可以访问这个成员了
struct MyStruct {
    int data;
};
void set_data(struct MyStruct* ms, int i) {
 	ms->data = i;   	// 可以使用 -> 运算符来访问结构体指针变量的成员
}
int get_data(struct MyStruct* ms) {
    return ms->data;
}
```

枚举和结构体非常类似，也可以进行声明，但在定义前都是不完整类型。

```c
// 声明了枚举类型 MyEnum，但是它现在还不是一个完整类型
enum MyEnum;
int get_num(enum MyEnum* e);

enum MyEnum {
    One, Two, Three
};
int main() {
    enum MyEnum e = One;
    printf("%d", get_num(&e));
    return 0;
}
int get_num(enum MyEnum* e) {
    return *e;
}
```

### 作用域和变量的存储类型

**C** 的 **作用域（Scope）** 是一个划分 **C** 文件不同部分和层级的概念，通过一对大括号 `{}` 来表示。作用域允许相互嵌套。不存在嵌套关系的作用域之间是相互“隔离”的，其中任一个作用域中定义的所有标识符不能再另一个作用域中引用；存在嵌套关系的作用域中，只有外部作用域的标识符能够被内部引用。值得一提的是，函数体也是一个作用域；在文件中“直接暴露”的作用域只能是函数。换句话说，所有作用域都是函数或者嵌套在函数中的作用域（或 **全局作用域（Global Scope）**，我们马上就会提到）。

```c
// 在文件中直接定义的标识符被称为处于 全局作用域 中，所有其它作用域都嵌套在它当中
int i;
void func() {				// 函数这里开始了一个作用域
    i = 10;					// 将全局作用域中的 i 赋值为 10
    {						// 开始了新的一个作用域
        int i;				// 内部作用域中的标识符会 覆盖 外部作用域中同名标识符的定义
        i = 20;
        printf("%d", i);	// 输出 20
    }						// 作用域结束，其中定义的标识符都将失效
    printf("%d", i);		// 输出 10
    int i;					// 覆盖了全局作用域中的 i
    i = 15;
    printf("%d", i);		// 输出 15
}
int main() {
    func();					// 所有函数都在全局作用域中
    return 0;
}
```

变量的 **存储类型（Storage Class）** 也和作用域有关。我们用下面的表来进行说明：

| 关键字                | 存储类型       | 说明                                                         |
| --------------------- | -------------- | ------------------------------------------------------------ |
| `auto`（可以省略）    | 自动存储类型   | 该变量只能定义在非全局作用域，且会在其作用域开始时分配内存，结束时释放内存 |
| `extern` （可以省略） | 全局存储类型   | 该变量只能定义在全局作用域，其在程序中全程存在               |
| `static`              | 静态存储类型   | 该变量在程序中全程存在，但其标识符只能在所在文件及作用域中有效 |
| `register`            | 寄存器存储类型 | 该变量和自动存储类型的变量基本一致，只不过会直接使用寄存器而非内存 |

下面我们将用一些例子具体描述上面这四种存储类型的特征：

```c
// example.c
int global_var;				// 全局变量的定义
extern int external_var;	// 全局变量的声明。注意这不是一个定义！
static int static_var;		// 静态全局变量的定义

int main() {
    auto int auto_var;			// 自动变量，也称为局部变量，这里的 auto 可以被省略
    static int func_static_var;	// 函数中的静态变量，其也存在于整个程序运行过程中，但是只有所在函数能够引用这个标识符
    extern int another_var;		// 全局变量的声明；与定义不同，声明出现的地方并没有限制
    
    int* get_local();			// 声明了两个函数
    int* get_static();
    printf("%d", *get_local());	// 错误，因为 get_local 返回了一个自动变量的指针，自动变量在函数返回后就会被释放
    printf("%d", *get_static());// 没有问题
    
    register int register_var;	// 寄存器变量
    printf("%p", &register_var);// 错误，不能对寄存器变量取地址
 	return 0;   
}
int* get_local() {
    int temp;
    temp = 10;
    return &temp;			// 返回自动变量的地址是危险的！
}
int* get_static() {
    static int temp;
    temp = 10;
    return &temp;
}
```

```c
// external.c
int external_var;			// 定义了全局变量 external_var，这样 example.c 中的声明就有了保障
extern int static_var;		// 错误，其它文件中不存在全局变量 static_var 的定义（事实上在 example.c 中它定义为静态变量）
```

### 函数

函数是一个子程序，事实上所谓的主程序就是前文中经常出现的 `main` 函数。程序开始运行时，`main` 函数会被调用，然后其中还可能调用其它的函数。

```c
int process(int i) {
 	return i * 2 + 1;   
}
int main() {
    printf("%d", process(10));	// 通过括号调用函数，输出 21
    return 0;
}
```

函数的参数会在传递时进行拷贝，因此传入的参数是不能通过函数进行修改的：

```c
void inc(int i) {	// i 在复制之后才传入函数
 	++i;   
}
int main() {
    int n;
    n = 0;
    inc(i);			// 这个函数不能改变任何内容
    return 0;
}
```

但是，指针类型存储的是地址，因此在复制过后，其内容（地址）不受影响，可以通过这个方式修改函数外面的值：

```c
void inc(int* i) {
    ++*i;				// *i 将 i 存储的地址处的内存取出，我们称为解引用
}
int main() {
    int n;
    n = 0;
    int(&n);			// 将地址传入
    printf("%d", n);	// 输出 1
    return 0;
}
```

**C** 语言中经常利用这个性质来将函数参数作为额外的返回值。函数的返回值也是通过拷贝传到调用处，因此返回一个自动变量并不会造成内存错误，但是返回一个自动变量的指针则是非常危险的。

函数和变量类似，可以被声明为静态的。因为函数一定定义在全局作用域，因此静态函数的意义就是其标识符只能在当前文件可见的函数。除此之外，还可以将函数声明为 **内联的（Inline）**。内联函数会建议编译器在函数调用处将其定义展开，从而避免了函数调用的开销。需要注意 `inline` 并非和 `static` 、`extern` 并列（它并不是存储类型修饰符），因此可能会出现 `static inline` 或 `extern inline` 这样的用法。

```c
inline int add(int a, int b) {
    return a + b;
}
inline void wrong() {
 	static int x;		// 错误  
}
```

