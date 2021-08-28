# Effective Modern C++ 笔记

[TOC]

这篇是对 *Effective Modern C++ (Scott Meyers)* 的阅读笔记，其对 **C++11** 之后 **C++** 语言的主要编码思路做出了建设性的建议。我将严格按照原书的章节分化，整理罗列出个人在意的知识点并给出自己的心得。

## 类别推导

### 模版类型推导

**C++** 的类型推导早在 **C++98** 就已经运用于模版了，程序员不需要显式给出模版函数的模版参数类型，只要能从所给的参数中推导出它们：

```cpp
template<typename T>
void print(T arg) {
    std::cout << arg;
}
int main() {
    print(1);		// 调用 print<int>(1)
    print(1.0);		// 调用 print<double>(1.0)
 	return 0;   
}
```

上面中 `print` 两次实例化出来的函数没有任何歧义，都能够从参数推导类型。这是一个相当方便的设定。不过，当形参存在修饰符（比如引用修饰符）时，情况会变得有些复杂：

1. 模版函数的形参是一个指针或引用（但不是转发引用）：

   这种情形的类别推导是最直接的，将最外层的引用褪去后，剩下的类型会成为推导的类型：

   ```cpp
   template<typename T>
   void f(T& param);
   template<typename T>
   void g(T* param);
   
   int main() {
       int x = 42;
       int const cx = x;
       int const& rcx = x;
       int&& rrx = 42;
       
       f(x);			// 实例化了 f<int>
       f(cx);			// 实例化了 f<int const>
       f(rcx);			// 实例化了 f<int const>
       f(rrx);			// 实例化了 f<int>
      	
       int* px = &x;
       int const* pcx = &x;
       int const*& rpcx = pcx;
       
       g(px);			// 实例化了 g<int>
       g(pcx);			// 实例化了 g<int const>
       g(rpcx);		// 实例化了 g<int const>
    	return 0;   
   }
   ```

   可以看到内层的 `const` 得到了保留。这是非常符合直觉的类型推导方式。

2. 模版函数的形参是一个转发引用：

   转发引用的特别之处在于它可以区别对待左值和右值。如果将左值传给模版函数的转发引用形参，会实例化一个左值引用，参数也同样变成一个左值引用（这略有些奇异）：

   ```cpp
   template<typename T>
   void f(T&& param);		// 转发引用和右值引用语法相同，但是在带有模版参数时都视为转发引用
   
   int main() {
       int x = 42;
       int const cx = x;
       int const& rcx = x;
       int&& rrx = 42;
       
       f(x);			// 实例化了 f<int&>，参数类型是 int&
       f(cx);			// 实例化了 f<int const&>，参数类型是 int const&
       f(rcx);			// 实例化了 f<int const&>，参数类型是 int const&
       f(rrx);			// 实例化了 f<int&>，参数类型是 int&；这里需要注意的就是右值引用是左值（因为所有引用都是左值）
       f(42);			// 实例化了 f<int>，参数类型是 int&&
    	return 0;   
   }
   ```

   这是唯一一种能在模版推导中得到引用类型的情形。

3. 模版函数的形参不存在任何修饰符：

   此时所有外层的修饰符都会被忽视（先褪去一层引用修饰符，再褪去一层 `const` 及 `volatile` 修饰符）：

   ```cpp
   template<typename T>
   void f(T param);
   
   int main() {
       int x = 42;
       int const cx = x;
       int const& rcx = x;
       int&& rrx = 42;
       int const* const cpcx = &cx;
       
       f(x);			// 实例化了 f<int>
       f(cx);			// 实例化了 f<int>
       f(rcx);			// 实例化了 f<int>
       f(rrx);			// 实例化了 f<int>
       f(cpcx);		// 实例化了 f<int const*>
    	return 0;   
   }
   ```

最后让我们看看两个自带 **退化（Decay）** 的类型：数组和函数。当它们传入上面第三种形参类型时会分别退化为指向首个元素的指针以及指向其自己的函数指针，此外的情形则能够被推导为数组引用及函数引用：

```cpp
template<typename T>
void f(T& param);
template<typename T>
void g(T param);

int main() {
    int arr[3] = { 1, 2, 3 };
    void foo();
    
    f(arr);				// 实例化了 f<int [3]>，参数类型是 int (&)[3]
    f(foo);				// 实例化了 f<void ()>，参数类型是 void (*)()
    g(arr);				// 实例化了 f<int*>
    g(foo);				// 实例化了 f<void (*)()>
    return 0;
}
```

### `auto` 类型推导

**C++11** 引入了 `auto` 关键字的全新含义（此前则是继承 **C** 语言中毫无存在感的自动变量修饰符），让类型推导能够出现在各种语句当中。总体来讲，`auto` 类型推导的规则和模版类型推导一致，左侧对等于模版函数的形参声明，右侧对等于模版函数的实参。

```cpp
int main() {
    auto x = 10;			// x 被推断为 int
    auto const cx = 42;		// cx 被推断为 int const
    
    auto& rx = x;			// rx 被推断为 int&
    auto& rcx = cx;			// rcx 被推断为 int const&
    auto const& rcx2 = x;	// rcx2 被推断为 int const& 
    
    auto&& rx2 = cx;		// rx2 被推断为 int const&
    auto&& rrx = 42;		// rrx 被推断为 int&&
    auto&& rx3 = rrx;		// rx3 被推断为 int&
    
    int arr[3] = { 1, 2, 3 };
    void foo();
    auto a = arr;			// a 被推断为 int*
    auto* a = arr;			// a 被推断为 int*
    auto& a = arr;			// a 被推断为 int (&)[3]
    auto f = foo;			// f 被推断为 void (*)()
    auto* f = foo;			// f 被推断为 void (*)()
    auto& f = foo;			// f 被推断为 void (&)()
    return 0;
}
```

`auto` 机理上唯一有别于模版类型推导的就是对于大括号的列表的类型推断：

```cpp
int main() {
    int x1 = 10;			// x1 被声明为 int
    int x2(10);				// x2 被声明为 int
    int x3{10};				// x3 被声明为 int
    int x4 = { 10 };		// x4 被声明为 int
    auto y1 = 10;			// y1 被推断为 int
    auto y2(10);			// y2 被推断为 int
    auto y3{10};			// y3 被推断为 std::initializer_list<int>
    auto y4 = { 10 };		// y4 被推断为 std::initializer_list<int>
 	return 0;   
}
```

这实际上还是一个对模版推导的补完；如果对模版函数中传入大括号的列表，是不能自动推断出其类型的。而 `auto` 可以根据列表的元素类型推断为例如 `std::initializer_list<int>` 的类型。只不过这样就没发让 `auto` 初始化的类型和显式声明类型的初始化的类型完全一致了，需要注意这一点。

**C++11** 为了统一变量初始化的语句而引入了 `{}` （列表初始化）这个机制，却又因为对同时引入的 `std::initializer_list<>` 的“过多偏爱”造成一些意想不到的情况。我们后面可能还会提到这一点。

### `decltype`

`decltype` 是 **C++11** 引入的新关键字，它能够接收一个表达式，然后返回一个具体的类型，其中也反应了某种类型推导机制。令人沮丧的是，这种机制和我们之前提到的模版推导和 `auto` 推导都有所不同。

首先让我们看看普通的情形：

```cpp
int const cx = 42;			// decltype(cx) 是 int const
void foo(int const& x);		// decltype(foo) 是 void foo (int const&)
struct Point {
    int x, y;				// decltype(Point::x) 是 int
} p;						// decltype(p) 是 Point
std::vector<int> v(10);		// decltype(v) 是 std::vector<int>
							// decltype(v[0]) 是 int&
```

似乎没有什么不寻常的事情？上面所有类型推导都十分规矩，甚至有点过于规矩，比如函数没有退化为指针、引用没有被褪去。这种忠实于表达式真实类型的性质让 `decltype` 一度成为比较流行的模版函数返回值类型的推断：

```cpp
// 下面这种函数声明语法是 C++11 引入的，可以用 auto 作为返回类型占用符，然后将真正的返回类型用 -> 结构显式给出
// 在使用这种尾序语法时，可以利用 decltype 判断某个表达式的类型。需要注意表达式中不能出现没有引入的标识符
template<typename Container, typename Index>
auto auth_and_access(Container& c, Index i) -> decltype(c[i]) {
    authenticate_user();
    return c[i];
}
```

**C++14** 起，我们不需要显式给出尾序的返回类型，编译器可以根据 `return` 语句来判断返回类型：

```cpp
template<typename Container, typename Index>
auto auth_and_access(Container& c, Index i) {
    authenticate_user();
    return c[i];
}
```

呃，不过需要指出，这种情况下会使用 `auto` 的类型推导规则。而我们之前认识到它会褪去一层引用，这就和我们的初衷不符了。这也是为什么 **C++14** 同时引入了新的尾序函数声明的占位符 `decltype(auto)`。用它代替 `auto` 可以得到和之前一样的结果：

```cpp
template<typename Container, typename Index>
decltype(auto) auth_and_access(Conatiner& c, Index i) {
 	authenticate_user();
    return c[i];
}
```

实际上，`decltype(auto)` 可以用在所有 `auto` 能够使用的地方，也就是可以直接借用它来在任何地方进行类型推导：

```cpp
int main() {
	decltype(auto) x = 10;			// x 被推断为 int（等同于 decltype(10)）
    decltype(auto) const cx = 10;	// 错误，decltype(auto) 四周不能接其它修饰符了
    int const c = x;
    decltype(auto) cx = c;			// x 被推断为 int const
    int arr[3] = { 1, 2, 3 };
    decltype(auto) rx = arr[0];		// rx 被推断为 int&
    int&& temp = 42;
    decltype(auto) rrx = temp;		// rrx 被推断为 int&&
 	return 0;   
}
```

现在要开始讲述 `decltype` 比较神奇的特性。我们在前面的例子中看到，对一个变量进行 `decltype` 时，总是能够得到其本身的类型（尽管它是一个左值）；但对比如 `arr[k]` 进行 `delctype` 就会得到一个左值引用。这是因为 `decltype` 规定对于所有非平凡的左值表达式，都保证产生一个左值引用。因此 `decltype(x)` 是 `x` 的真实类型，而 `decltype((x))` 就可能会加一个引用符号。这个特点适用于所有 `dectlype` 类型推导规则出现的场景，包括 `decltype(auto)`。

### 如何查看类型推导结果

用 **Visual Studio** 就对了！这一节就这一点最重要，可以翻到下一章了。

开个玩笑，虽然宇宙第一 **IDE** 不见得是吹的，但是我们也要需要一些更轻量且巧妙的方法来获得类型推导结果。

第一种是借用编译器的报错，当模版实例化出现错误时，编译器会给出实例化相关的信息：

```cpp
// 只给出类的声明，实例化是会报错
template<typename T>
class TypeDisplay;

int main() {
    int x = 10;
    TypeDisplay<decltype(x)> type1;
    TypeDisplay<decltype((x))> type2;
    return 0;
}
```

也可以通过古老的 `typeid` 运算符在运行时得到类型信息。`typeid` 会接受一个值或类型并返回 `std::type_info` 对象，调用其 `name` 方法就能得到一段表示其类型的唯一字符串。只不过不同编译器产生的字符串可能大不相同，且不是那么可读：

```cpp
int main() {
    int x = 10;
    int const cx = x;
    int* px = &x;
    int& rx = x;
    int const* const cpcx = &cx;
    struct Temp {} t;
	std::cout << typeid(x).name() << '\n';		// 可能输出 i。这大体是指代 int
    std::cout << typeid(cx).name() << '\n';		// 可能输出 i。所以最外层的 const 被褪去了
    std::cout << typeid(px).name() << '\n'; 	// 可能输出 Pi。可能是 Pointer to int 的意思
    std::cout << typeid(rx).name() << '\n';		// 可能输出 i。所以最外层的引用也被褪去了
    std::cout << typeid(cpcx).name() << '\n';	// 可能输出 PKi。呃，可能是 Pointer to K(C)onst int 的意思
    std::cout << typeid(t).name() << '\n';		// 可能输出 Z4mainE4Temp。这是什么？？
	return 0;
}
```

可以发现 `typeid` 的行为和 `auto` 类型推导非常相似，但可读性令人堪忧。一个替代品是 **Boost** 库的 `type_index.hpp` 头文件，其中包含了一个不会褪去 `const` 等修饰符的 `type_id_with_cvr<>` 函数模版。

## `auto`

### 优先使用 `auto` 而非显式类别声明

让我们看看没有 `auto` 的 **C++11** 会出现什么样的代码：

```cpp
template<typename F, typename T>
void apply(F f, T t) {
 	f(t);   
}

int main() {
    int x;			// 呃，定义了一个 int 变量，却没有初始化它，它也许是一个混乱的值
    std::function<void (int)> f = [] (int x) { std::cout << x; };	// 可以使用 std::function<> 来绑定 lambda
    apply(f);
    return 0;
}

template<typename It>
void foo(It begin, It end) {
 	while (begin != end) {
     	typename std::iterator_traits<It>::value_type curr = *begin;	// 呃，这真的太长了
        // 省略具体操作
    }
}
```

现在引入了 `auto`，上面的代码都会变得更好：声明语句变短了，且强制其显式初始化；也不需要再用缓慢低效的 `std::function<>`。

```cpp
int main() {
 	auto x;			// 错误，auto 类型推导必须初始化变量
    auto x = 10;	// 没有问题
    auto f = [] (int x) { std::cout << x; };		// 将 lambda 绑定于一个变量。至于其类型，我们不需要关心
}
```

从 **C++14** 起，我们甚至可以将函数和 lambda 的参数类型声明为 `auto`，此时其行为非常像一个模版函数：

```cpp
auto inc = [] (auto x) { return x + 1; };
// 下面的模版函数和上面的 lambda 行为基本一致
template<typename T>
auto inc_templ(T x) {
    return x + 1;
}
```

`auto` 在大多数情况下都能大幅度减少检查类型正确性的工作，且能避免一些看似理所当然实则错误的想法，比如下面这个例子；

```cpp
int main() {
    std::unordered_map<std::string, iknt> m;
    // 需要注意 unordered_map 中存储的实际上是 std::pair<std::string const, int>
    // 因此这里会发生 std::string 的复制，非常浪费资源
    for (std::pair<std::string, int> const& p : m) {
        // 省略操作
    }
    // 这里 p 自动推断为 std::pair<std::string const, int> const&，就没有任何问题了
    // 最主要非常简洁
    for (auto const& p : m) {
     	// 省略操作   
    }
 	return 0;   
}
```

### 当 `auto` 推导的类型不符合要求时，使用显式类型声明

书中举例是代理结构造成的类型推断失误，我们可以用一个比较抽象的例子讲述：

```cpp
class Real {
    // 省略定义
};
class Proxy {
    Proxy& method1() { /* 省略定义 */ }
    Proxy& method2() { /* 省略定义 */ }
    // 这是为了简便的隐式类型转换
  	operator Real() const {
        // 省略定义
    }
};

void foo(Real const& r) {
 	// 省略定义   
}
int main() {
    // 利用 Proxy 类型进行运算，但是结果会返回给一个 Real 类型的变量
    Real result1 = Proxy().method1().method2();
    // 然而如果使用 auto，结果并不会隐式转换为 Real 类型，这可能会引发后面的严重错误
    auto result2 = Proxy().method1().method2();
    foo(result2);		// 类型错误
    // 当然我们可以利用强制类型转换来得到需要的类型
    auto result3 = static_cast<Real>(Proxy().method1().method2());
    return 0;
}
```

## 转向现代 **C++**

### 在创建对象时区分 `()` 和 `{}`

在 **C++** 中，以复杂而臭名昭著的特性中，变量的初始化算是需要最早接触的之一了。普遍情况下，一个变量有五种初始化的格式：

- 只给出类型和标识符，即 `type var;` 的形式
- 使用圆括号，即 `type var(init);` 的形式
- 使用等号，即 `type var = init;` 的形式
- 使用大括号，即 `type var{init};` 的形式
- 使用等号和大括号，即 `type var = { init };` 的形式

其中后面两种作为广泛的初始化语句在 **C++11** 前并不存在，最后一种在此前仅用于数组和结构体变量初始化。这里面比较令人迷惑的就是不带初始值的初始化和利用等号的初始化格式，前者会让新手认为是一个变量声明，后者会被误解为一个赋值操作。实际上，前者对变量进行默认初始化，而后者等同于圆括号的形式。

圆括号的初始化格式看似很美好，却依然存在一个问题：`type var();` 会被视作一个函数的声明而非一个变量的默认初始化；另一方面， `type var;` 无法预测一些内置类型的初始值。因此我们需要一个保证语法一致，且能够对任意类型进行默认初始化或零初始化的语法，这也就是大括号的引入由来。

```cpp
struct MyClass {   
   	int x(0);		// 错误，不能这样初始化成员
    int y = 0;		// 没问题
    int z{0};		// 没问题
    int w = {42};	// 没问题
}

int main() {
 	int x{};		// 将 x 初始化为 0，等价于 int x = int();
    int y{42};		// 将 y 初始化为 42，等价于 int y(42);
    int z = { 10 };	// 将 z 初始化为 10，等价于 int z = 42
    // 相比之下：
    int f();		// 声明了一个函数 f
    int g(42);
    int h = 10;
}
```

因此大括号是最“通用”的初始化方式，它能够融合 **C++11** 以前的所有初始化形式，并 *基本* 达到相同的结果。为什么强调是基本，因为 **C++11** 引入了 `std::initializer_list<>` 模版，并为其开了后门：所有大括号包含的列表都会以更高优先级被视作一个 `std::initializer_list<>` ，这个优先级。这就造成了下面这种微妙的区别：

```cpp
struct MyClass {
  	MyClass(int i, double d) {}
    MyClass(std::initializer_list<double> list) {}
};
int main() {
    std::vector<int> v1 = { 1, 2, 3 };	// 调用 std::vector<int>(std::initializer_list<int>)
    std::vector<int> v2(3, 0);			// 调用 std::vector<int>(size_type, int)
    std::vector<int> v3{3, 0};			// 调用 std::vector<int>(std::initializer_list<int>)
    
    MyClass mc1{1, 2.0};				// 调用 MyClass(std::initializer_list<double>)
    MyClass mc2{1, 2.0lf};		
    	// 错误，其认定调用 MyClass(std::initializer_list<double>)，但这里传入的 long double 不能隐式类型转换
    return 0;
}
```

简单解释这个性质，就是大括号初始化在没有定义 `std::initializer_list<>` 为参数的构造函数时，等同于用圆括号的直接初始化，否则只要大括号内的所有对象类型都能被显式转换为指定的类型，就会被当作 `std::initializer_list<>` 处理（且如果不能够隐式类型转换，还会产生错误。这点其实特别离谱），需要特别注意。

但是，如果是空的大括号，会被编译器理解为默认初始化，而非空的 `std::initializer_list`<>`：

```cpp
struct MyClass {
    MyClass() {
        std::cout << "Default initialization";
    }
    MyClass(std::initializer_list<int> list) {
        std::cout << "List initialization";
    }
};
int main() {
	MyClass mc1{};			// 调用 MyClass()
    MyClass mc2 = {};		// 调用 MyClass()
    MyClass mc3({});		// 调用 MyClass(std::initializer_list<int>)
    MyClass mc4{{}};		// 调用 MyClass(std::initializer_list<int>)
    MyClass mc5 = { {} };	// 调用 MyClass(std::initializer_list<int>)
    return 0;
}
```

呜哇，这还真的是令人头疼。明明引入了一个看似能够统合所有风格的初始化方法，却因为对 `std::initializer_list<>` 的偏爱而产生难以预知的问题；默认初始化的情形也显得前后不一致。个人使用的风格是，仅在默认初始化以及使用 `std::initializer_list<>` 作为参数时才会使用大括号，除此之外只使用小括号的直接初始化。

```cpp
int main() {
    std::ostringstream oss{};			// 默认初始化
    std::vector<int> v1(3, 10);			// 直接初始化，将向量初始化为 3 个 10
    std::vector<int> v2 = { 1, 2, 3 };	// 列表初始化
 	return 0;   
}
```

这样能够避免任何歧义，且确保所有变量正确地初始化。

### 优先选用 `nullptr` 而非 `0` 或 `NULL`

**C++11** 之前沿用了 **C** 语言的 `NULL`，其定义是基于实现的，但通常是一个整型。这会导致存在参数为整型和指针类型的函数重载时，会优先调用整型参数的函数，这就和预期不符了。相比之下，`nullptr` 是独特的  `std::nullptr_t` 类型，其被设计为能够自动转换为任何指针类型：

```cpp
void foo(long long l):
void foo(void* ptr);
void bar(std::unique_ptr<int> ptr);
int main() {
    foo(NULL);		// 可能会调用 foo(long long)
    foo(nullptr);	// 一定会调用 foo(void*)
    bar(NULL);		// 错误，NULL 不是一个指针类型
    bar(nullptr);	// 没有问题
    return 0;
}
```

### 优先使用 `using` 而非 `typedef`

`using` 是 **C++11** 引入的别名声明关键字，它可以将某个标识符声明为一个类型。相比继承自 **C** 语言的 `typedef` 语句，`using` 在语法上更加可读，比如下面这几个例子：

```cpp
// 声明了 func_type1 类型
typedef void (*func_type1)(int, double);
// 声明了 func_type2 类型
using func_type2 = void (*)(int, double);
```

`using` 语句中等号的左侧是新声明的标识符，而右侧是想要声明为别名的类型，相比 `typedef` 要清楚很多（虽然依然很奇怪）。

除了这点，可以定义模版 `using` 别名，而 `typedef` 就不行：

```cpp
template<typename T>
using vector_iterator1 = typename std::vector<T>::iterator;
// 如果使用 typedef 只能这样写
template<typename T>
struct vector_iterator2 {
  	typedef std::vector<T>::iterator type;
};
int main() {
 	vector_iterator1 it1{};
    // 此时必须要使用 typename 关键字，因为所有类型中定义的类型及别名都需要 typename 才能判断为类型
    typename vector_iterator2::type it2{};
}
```

关于啰嗦的 `typename`，**C++14** 对 `<type_traits>` 中的一些模版如 `std::remove_reference<>` 定义了 `using` 别名，能够省略一些冗余：

```cpp
namespace std {
template<typename T>
using remove_reference_t = typename remove_reference<T>::type;
}
int main() {
 	typename std::remove_reference<int&>::type i{};		// C++14 前的写法
    std::remove_reference_t<int&> j{};					// C++14 之后的写法
}
```

这显著减少了无意义的代码量。

### 优先选用限定作用域的枚举类型

**C++** 继承了 **C** 语言的无作用域枚举，也即枚举中的所有标识符都仿佛定义在枚举类型定义所在的作用域中。这会造成命名空间污染：

```cpp
enum Color { black, white, red };
void foo(int i);
int main() {
    auto white = false;			// 错误，双重定义
    foo(white);					// 可以通过，因为无作用域枚举类型可以隐式转换为其它类型
 	return 0;   
}
```

这并不符合 **C++** 设计命名空间以避免标识符污染，以及避免隐式类型转换的思想。于是在 **C++11** 中引入了 `enum class` 的概念：

```cpp
enum class Color { black, white, red };
void foo(int i);
int main() {
	auto white = Color::white;		// 没有问题 
    foo(white);						// 错误，类型不一致
    foo(static_cast<int>(white));	// 枚举类型依然可以转换为其它整数类型，这是因为它本质上就是一个整型
}
```

每个枚举类型本质都是一个整型，我们可以用类似于类型继承的语法来显式指定其本质的类型。通过 `std::underlying_type<>` 能得到枚举类型的本质类型。

有时候会感觉限定作用域的枚举类型过于啰嗦，这个时候可以写一个帮助函数：

```cpp
template<typename E>
constexpr typename std::underlying_type<E>::type toUType(E enumerator) noexcept {
 	return static_cast<typename std::underlying_type<E>::type>(enumerator);   
}
enum class Color { black, white, red };
void foo(int i);
int main() {
 	foo(toUType(Color::white));
    return 0;
}
```

### 优先使用删除函数，而非 `private` 未定义函数

通常，如果我们不希望用户调用某个函数（或成员函数），只需要不声明它即可。然而， **C++** 有时会自动生成一些成员函数。
