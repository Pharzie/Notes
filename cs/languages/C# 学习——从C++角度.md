# **C#** 学习 —— 从 **C++** 角度

[toc]

最近出于兴趣开始接触 **C#** 。为了更好地理解一些语法特征，我也会主动尝试对标 **C++** 中同等的功能，所以不由得会有两者间的比较；并且早就对它优于 **Java** 的语言特性有所耳闻，于是眼睛会更加叼一些。

参考书是《C#图解教程（第5版）》（下文会简称为《C#教程》），本笔记的所有章节内容都是根据原书，以个人关注点为导向的东西，并不能反映原书的全部知识点。

## **C#** 和 **.NET** 框架

~~警告：本节存在大量英文缩写，可以酌情放松一些，但至少对它们有个眼熟。~~

**C#** 是为了 **.NET** 而生的，这从它的诞生历史就能看出。20 世纪末，**Windows** 平台的编程有好几个门派， **Win32 API**、**MFC（Microsoft Foundation Class）**，或 **COM（Component Object Model）**。但是它们或多或少都有不可忽视的问题。因此微软在 21 世纪初推出了跨平台、安全、采用行业标准通信协议的框架，允许代码将其作为运行环境。下面是 **.NET** 的一些重要组件：

- **CLR（Common Language Runtime）**：这是代码的执行环境，进行程序的内存管理、代码安全认证以及代码执行、线程和异常的管理。
- **BCL（Base Class Library）**：通用的一个大型类库，可以在程序中调用。
- 编程工具：包括“宇宙第一**IDE（Integrated Development Environment）**” **Visual Studio**、与 **.NET** 兼容的编译器、调试器、和一些 **Web** 开发工具，比如 **ASP.NET**。

现在，我们在编写代码（可能调用 **BCL**）后，通过编程工具进行编译和调试，最后运行在 **CLR** 上。这里的代码可以是任何与 **.NET** 兼容的语言，比如 **C#**、**Visual Basic .NET**、**F#**、**C++/CLI**（我不是很想承认这个是 **C++**，当然它本身并不是）等。这样的公共平台是非常难得的，因为编程者不需要再针对桌面端、移动端、**Web**等多个场景设计不同的代码；取而代之的，基本只需要面向 **.NET** 编程即可（脏活累活都交给框架实现者）。同时， **CLR** 中有一个内存管理机制，即闻名的 **GC（Garbage Collector）**，它会检查内存中不被引用的对象并回收。这能从内存管理的泥沼中解放程序员的双手。此外，同被 **.NET** 支持的语言之间可以互相调用模块，仿佛毫无隔阂。

从这里看，**.NET** 和 **Java** 语系的 **JVM（Java Virtual Machine）** 非常相似（只能说一模一样）。先不论这两者之间的优劣，至少 **.NET** 在多个方面能把前文提到的三个老大哥打趴下的，因此它也确实受到了不少开发者的欢迎。

和 **Java** 源码被编译为字节码类似，**.NET** 上的语言会被编译为 **CIL（Common Intermediate Language）** 组成的程序集。这段代码依然是平台无关的，并且携带了程序中的类型和安全信息。随后，在程序被调用时，再通过 **JIT（Just-In-Time）** 编译器将程序集分步编译为机器相关的代码，再执行。**JIT** 进行编译的过程全程是受到 **CLR** 管理的。

为了像前文提到那样让不同语言在 **.NET** 上做好兄弟，微软设计了 **CLI（Common Language Infrastructure）** 作为这些语言都要遵守的规范，其中包括 **CLR**、**CLS（Common Language Standard）**、**BCL**、**CTS（Common Type System）**、**CIL**，以及元数据定义和语义。目前对这些从字面理解尚浅，但是可以看到为了让迥异的多个语言友好相处是要费比较大功夫的。不过这些语言都是微软自己家的，调教起来阻力不大（**C++** 也被魔改成 **C++/CLI** 了）。

《C#教程》在本节末吹了一下 **Windows** 和历代推的跨平台的环境，并且用 **Stackoverflow** 的开发者调查报告表明 **C#** 的流行和受欢迎程度都超过更加流行的 **JavaScript**、**SQL** 和 **Java**。总之未来可期就对了。呃，我是不反对的。不过语言的流行程度我无法判断，语言的友好程度、趣味性和创造力我还是能有所体会的，那么就让我拭目以待吧！

## **C#** 和 **.NET Core**

总结就是移动端和 **Web** 侵食了桌面应用的不少份额，早期专注于桌面端的 **.NET** 可能自己打不太行，因而微软推出了衍生产品 **.NET Core** 来同样支持 **Web** 开发和 **Linux**、**MacOS**，并收购了 **Xamarin** 来支持移动端。现在局势可能有点复杂，但是总之 **C#** 在这些上面都能较好的支持，因此学 **C#** 就对了。

## **C#** 编程概述

**C#** 的程序主体结构是 **命名空间（Namespace）** 包含着 **类（Class）**，其中再包含着 **成员函数（Member Function）** 和 **成员（Member）**。程序从一个 `Main` 函数中开始执行。下面是一个你好世界的例程：

```csharp
using System;		// 相当于导入一个包
namespace MyNamespace {
    class MyProgram {		// 所有函数和变量都需要出现在类当中
        static void Main() {
            Console.WriteLine("Hello, world!");	// Console 是在 System 命名空间的类，WriteLine 是一个方法
        }
    }
}
```

《C#教程》中罗列的 **C#** 的关键字足足有 77 个之多，其中不少是我难以猜测作用的新面孔。除此之外还有 26 个 **上下文关键字（Context-Sensitive Keyword）**，真的相当之多。**C++** 中也有上下文关键字的概念，能够立刻想到的是诸如 `override` 和 `final` 这样 **C++11** 之后添加的，只有在特定位置才会被识别为关键字。

**C#** 中的字符串可以进行格式化输出：

```csharp
// 这里 {0}，{1} 是指代后面的替换值，这里 {0} 就是 a，{1} 就是 b
// 后面的替换值可以是任何类型的，格式化串中的替换值顺序可以是任意的且允许重复
Console.WriteLine("Two numbers: n1={0}, n2={1}", a, b);
// (C# 6.0) 如果替换的是变量，可以使用所谓的字符串插值，用字符串前缀 $，里面所有大括号中都会视作代码表达式
Console.WriteLine("My name is {name}, {age} and live in {city}{address}");
```

哎，**C++** 馋疯了。用 `std::ostream` 那点东西根本不舒服；否则就是用远古的 `printf`，但也远比不上 **C#** 这样灵活。**C++23** 将要纳入标准库的 `std::format<>` 也更加相似于 **C#** 传统的格式化方式（上面的第一种），并不能随便控制替换值顺序和重复出现，不过在其它的方面更加强大一点。

此外，格式化字符串对数字有更加细微的控制，可以在 `{}` 中使用冒号后加上格式说明符的方式指定数字的输出格式，比如 `{abc, F4}` 表示输出四位小数的浮点数。这里格式说明符太多，就不展开说明了。

## 类型、存储和变量

**C#** 有 16 种预定义类型，其中包含整数、浮点、布尔、字符等简单类型，以及 `object`、`string` 和 `dynamic` 三种非简单类型，其中 `object` 是所有其它类型的基类。除了用于动态语言编写程序集的 `dynamic` 外，其余的类型都能在 **.NET** 框架中找到类型对应。

**C#** 也对自定义类型进行了分类，包括 `class`、`struct`、`array`、`enum`、`delegate`（委托）和 `interface`（接口）。可以通过类型声明来创造自定义类型。除了数组和委托外，其它的结构中都可以声明成员。**C++** 和这个差别不太大，也包括 `class`、 `struct`、 `enum` 以及内置的数组类型；接口类似于 **C++** 中的抽象类；委托则类似于 **C++** 的函数指针。总之有进行类比的价值。

**C#** 将所有类型分为 **值类型** 和 **引用类型** 两种。前者只占用栈空间，而后者本质是一个栈上的指针指向堆上的内存空间。所有的内置简单类型、`struct` 和 `enum` 是值类型，此外都是引用类型。这点倒是和 **Java** 基本一致，但远不如 **C++** 灵活（我想让什么在栈上，它绝对不敢在堆上），不过权力越大责任也越大，为了 **GC** 还是放弃这点权力吧。

变量可以被分为 **局部变量**、**字段**、**参数** 以及 **数组元素**（这个分类怎么看怎么别扭）。其中局部变量是在函数内部定义的变量；字段是类中声明的变量，永远依附于一个类或者一个对象；参数是通过函数传递得到的变量，类似于局部变量；数组元素可能是局部变量也可能是类型的成员。

变量初始化的方式比较直接，即 `type varname = init_val`。不过 **C#** 继承了 **C++** 的“坏毛病”，对于局部变量并不会为其自动初始化。当然，访问一个未初始化的变量会产生异常。其余的情形，比如字段，都能够自动初始化，因此可以放心地使用。

## 类的基本概念

类是 **C#** 中极其常用的结构。下面是一个示例的 **C#** 类型定义：

```csharp
class MyClass {
    private int i = 10;		// private 和 C++ 中的含义一致，并且可以在类中初始化变量
    int j;					// class 中默认为 private，且可以自动初始化。此处为 0
    public string str;		// public 和 C++ 中的含义一致，此处初始化为 null（注意和 C++ 中初始化为 "" 不同）
}
class MyProgram {
	static void Main() {
     	MyClass mc = new MyClass();		// new 语句和 C++ 中的含义一致，不过 C# 的引用类型并不需要形如 * 的修饰符  
        mc.str = "abc";
        Console.WriteLine(mc.str);		// 输出 "abc"
    }
}
```

一提到 **Java** 和 **C#** 的访问修饰符就费劲儿，只要不是 `private`，所有变量或者函数前面都要写出来。相比之下 **C++** 的访问修饰标签简洁太多了。还有一个需要适应的，就是引用类型不需要指针修饰符 `*`，并且所有在对象上的操作都默认解引用过，这点比较省事但是有的时候显得模糊，比如“著名”的浅拷贝问题：

```csharp
class MyClass {
	private int i;
    public MyClass(int i_) : i(i_) {}	// 类初始化列表的用法和 C++ 一致
    public int get_i() { return i; }
}
class MyProgram {
 	static void Main() {
    	MyClass mc1 = new MyClass(10);
        MyClass mc2 = mc1;				// 这里是一个浅拷贝，只是对指针进行了初值
        mc2 = new MyClass(mc1.get_i());	// 这才是深拷贝，创建了新的对象并且将字段复制过去
    }
}
```

所以说等号的作用可能会造成一定迷惑。不过一个辅助判断方式就是只有出现 `new` 语句时才可能出现深拷贝。**C++** 在这点上分得很清楚：

```cpp
class MyClass {
private:
	int i;
public:
    MyClass(int i_) : i(i_) {}
    int get_i() { return i; }
};
int main() {
    MyClass* mc1 = new MyClass(10);		// 通过 new 表达式创造一个指向堆的指针
    MyClass* mc2 = mc1;					// 只复制了指针，因此没有新的 MyClass 对象
    mc2 = new MyClass(mc1->get_i());	// 这里才产生了新的对象，并且将新的指针赋值给 mc2
    delete mc1;
    delete mc2;
    return 0;
}
```

## 方法

这一部分和 **C++** 风格有些差异，需要提到的首先有两点：其一，**C#** 不支持变量隐藏，也就是在块结构内的变量不能和外面的变量拥有相同的名字；此外，在 **C# 7.0** 往后，可以在函数中定义函数，即局部函数（语法上更加丰富包容了，我其实一直不理解为啥 **C++** 不能允许局部函数）。

此外，**C#** 有千奇百怪的参数传递方式，这几乎让我改变对 **C#** 还算不错的第一印象：

### 参数传递

和 **C++** 一样，函数默认传递的都是值，即所谓 **值参数（Value Parameter）**，被传入的内容无论在函数中如何修改，是无法改变函数调用处的这个值的。但是因为存在引用类型这种东西，因此虽然引用本身没法改变，却可以改变引用的对象的值：

```csharp
class MyClass {
	public int val = 42;
}
class MyProgram {
 	void PassByValue(int a, MyClass mc) {	// 以值方式传入，即所有传入值都被浅拷贝了一遍
        a = 10;					// 不会影响调用处的的值
        mc.val = 10;			// mc 会被解引用，这里会影响调用处的值
        mc = new MyClass();		// mc 现在引用了一个新的对象，但是不会影响调用处的值
        mc.val = 20;			// mc 引用的新对象被修改了，但是没什么用
    }
    static void Main() {
        MyClass mc = new MyClass();
        int i = 42;
        PassByValue(i, mc);
        Console.WriteLine($"i = {i}, mc.val = {mc.val}");	// 输出 "i = 42, mc.val = 10"
    }
}
```

同时，**C#** 也支持通过引用传递，即所谓 **引用参数（Reference Parameter）**。这样，许多值类型就可以通过函数修改了：

```csharp
class MyClass {
 	public int val = 42;   
}
class MyProgram {
 	void PassByReference(ref int a , ref MyClass mc) {	// 用引用传递
    	a = 10;					// 因为是通过引用传递的，这里会影响调用处
        mc.val = 10;
        mc = new MyClass();		// 这里会影响引用本身！调用处的引用现在引用了新的对象
        mc.val = 20;
    }
    static void Main() {
     	MyClass mc = new MyClass();
        int i = 42;
        PassByReference(ref i, ref mc);		// 函数调用处也要用 ref 关键字传递，ref 后面不能是常量
        Console.WriteLine($"i = {i}, mc.val = {mc.val}");	// 输出 "i = 10, mc.val = 20"
    }
}
```

我们可以用一个 **C++** 程序来统一介绍这个特性：

```cpp
struct MyClass {
    int val;
}
int MyClass::val = 42;
void pass_by_value(int a, MyClass* mc) {
    a = 10;
    mc->val = 10;
    delete *mc;
    mc = new MyClass();
    mc->val = 20;
}
void pass_by_reference(int* a, MyClass** mc) {
    *a = 10;
    (*mc)->val = 10;
    delete *mc;
    (*mc) = new MyClass();
    (*mc)->val = 20;
}
int main() {
    int i = 42;
    MyClass* mc = new MyClass();
    pass_by_value(i, mc);
    // 丑哭了，为什么 C++ 格式化打印东西这么丑
    std::cout << "i = " << i << ", mc->val = " << mc->val << '\n';
    pass_by_reference(&i, &mc);
    std::cout << "i = " << i << ", mc->val = " << mc->val << '\n';
    delete mc;
    return 0;
}
```

接下来开始出现奇怪的参数类型了，首先是 **输出参数**。输出参数和引用参数基本一致，但是有几个额外检查：

- 需要是已经声明的变量
- 函数内在对输出参数进行赋值前不能被读取
- 函数在返回之前必须对所有输出参数进行赋值
- 调用函数处需要使用 `out` 修饰符

然后是 **输入参数**。其和引用参数基本一致，但是有一个额外要求：

- 函数内不能修改这个参数

```csharp
class MyClass {
 	public int val = 42;   
}
class MyProgram {
 	void PassInOut(out int sum, in int x, in int y) {
     	sum = x + y; 
    }
    void PassInOut2(out MyClass mc) {
		mc.val = 10;
    }
    static void Main() {
        int result;
        PassInOut(out result, 10, 42);
        Console.WriteLine($"result = {result}");	// 输出 "result = 52"
        PassInOut(out MyClass mc);					// (C# 7.0) 可以直接在调用处声明变量
        Console.WriteLine($"mc.val = {mc.val}");	// 输出 "mc.val = 10"
    }
}
```

在微软的官网上这样总结 `ref`、`out`、`in` 三种参数传递：

| **参数类型**      | 在函数中的行为            | 传递方式                                                |
| ----------------- | ------------------------- | ------------------------------------------------------- |
| 引用参数（`ref`） | *可能* 在函数中修改它的值 | 在传递处使用 `ref` 关键字                               |
| 输出参数（`out`） | *必须* 在函数中修改它的值 | 在传递处使用 `out` 关键字，也可以直接在调用处声明该变量 |
| 输入参数（`in`）  | *不能* 在函数中修改它的值 | 传递处和值参数无异                                      |

从某种角度上，**C#** 这样的设计用一种强制的方式抽象出函数输入和多个函数输出的模式。如果从 **C++** 角度来看，`in` 参数就是常引用 `const &`，能够达到一模一样的效果（考虑到 **C#** 的自动解引用，实际上也可以是 `const *`。`out` 参数在 **C++** 中则不存在等价对应。**C++** 除了拥有 `const` 之外，实际上缺乏对变量赋值（初始化）的强制。

最后让我们介绍 **参数数组**，它可以接受多个相同类型的参数，但有下列限制：

- 参数列表中至多只能有一个参数数组
- 参数数组必须是最后一个参数

为参数数组类型的参数传递参数时，可以是一个数组，或者是任意数量的参数。需要注意的是，因为可以传入数组，而数组是一个引用类型，所以 `null` 也可以被传入。

```csharp
class MyProgram {
 	int SumInt(params int[] vals) {
        int sum = 0;
     	if (vals != null && vals.length != 0) {
			for (int i = 0; i < vals.length; ++i) {
            	sum += vals[i];
            }
        }
        return sum;
    }
    static void Main() {
        int sum1 = SumInt(1, 2, 3, 4, 5);	// sum1 = 15
        int[] arr = { 1, 2, 3, 4, 5 };
        int sum2 = SumInt(arr);				// sum2 = 15
        int sum3 = SumInt();				// sum3 = 0
        int sum4 = SumInt(null);			// sum4 = 0
    }
}
```

比起直接传递数组，参数数组语法上更加简洁，在需要不定个数的同类型参数时比较好用。**C++** 中可以使用（过时的）可变参数函数，不过这个局限太大了（首先就是根本不知道有多少参数）。也可以使用初始化列表类型（**C++11**)，但是在调用处需要显示写出大括号，有时可能还要给出里面元素的类型。最通用的是使用模版参数包（**C++11**），不过它对类型的控制不是很严格，可能需要 `constexpr-if`（**C++17**）：

```cpp
int sum_int() {
    return 0;
}
template<typename First, typename... Rest>
int sum_int(First first, Rest... rest) {
    if constexpr (std::is_same_v<First, int>) {
        return first + sum_int(rest...);
    }
    return 0;
}
int main() {
    int sum1 = sum_int(1, 2, 3);		// sum1 = 6
    int sum2 = sum_int();				// sum2 = 0
    int sum3 = sum_int(1, "abc", 2, 3);	// sum3 = 6
    return 0;
}
```

如果需要强制输入的类型，可以使用模版非类型参数包：

```cpp
template<int... args> struct SumInt;
template<>
struct SumInt<> {
  	static constexpr int result = 0;  
};
template<int first, int... rest>
struct SumInt<first, rest...> {
  	static constexpr int result = first + SumInt<rest...>::result;  
};
int main() {
    int sum1 = SumInt<1, 2, 3>::result;	// sum1 = 6
    int sum2 = SumInt<>::result;		// sum2 = 0
 	return 0;   
}
```

不过用模版做这个确实有点大材小用（费劲儿），而且适用类型必须是合法的非类型模版参数，最重要的是需要编译期常量。因此还是前面一种方式比较合适。相比之下，**C#** 虽然引入了奇怪的关键字，但是相对更加优雅地解决了此类问题。

### 方法返回值

前面我们见到的函数返回值都是值返回值，其特点可以参考前面的值参数。也可以通过引用返回一个值：

```c#
class MyClass {
    private int val = 42;
    // 这个函数返回一个 ref 类型
    public ref int GetReference() {
        return ref val;							// 需要显式使用 ref 关键字
    }
    public void Display() {
        Console.WriteLine($"The value is {val}");
    }
}
class MyProgram {
    static void Main() {
        MyClass mc = new MyClass();
        mc.Display();							// 输出 "The value is 42"
        ref int rval = ref mc.GetReference();	// ref 类型变量左右都需要使用 ref 关键字
        rval = 10;
        mc.Display();							// 输出 "The value is 10"
    }
}
```

这里和 **C++** 的指针机制可以说是一模一样；通过这些使用 `ref` 的例子，能够发现使用在类型中的 `ref` 关键字等同于 **C++** 中的指针修饰符 `*`，而使用在表达式中的 `ref` 关键字等同于 **C++** 中的取地址运算符 `&`。结合前面的引用类型，我们可以将 `ref` 看作是显式的引用类型。此外，所有引用类型在使用时（不包括浅拷贝）都会自动解引用，因此才会出现上面奇怪的 `ref int rval = ref mc.GetReference()` 这样奇怪的东西。不过从 **C#** 本身的角度来看，倒是还算自洽。

```cpp
class MyClass {
public:
    int* get_reference() {
        return &val;
    }
    void display() {
        std::cout << "The value is " << val;
    }
private:
    int val;
};
int main() {
    MyClass mc = new MyClass();
    mc->display();
    int* rval = mc->get_reference();	// C++ 不会自动解引用（右侧的指针），因此这里正好赋值给左侧的指针
    *rval = 10;							// 需要解引用
    mc->display();
}
```

最后有几个注意事项：

- 不能返回空、常量、枚举、属性和指向只读位置指针的引用。简单概括就是 `ref` 只相关于可变的值，而属性（下一章会提到）本质是一个方法所以也不能返回（可能是其读写性质比较复杂因此这里没有进行适应）。所以说 `ref` 就等同于没有 `const` 修饰的指针，因此不能指向 `const` 类型。
- 不能返回局部变量，这一点和 **C++** 的返回局部引用是类似的。
- 如果返回引用的函数的返回语句没有使用 `ref` 关键字，那么只会返回一个值。这一点相当微妙，因为在调用处是无法意识到这一点的。
- `ref` 类型的变量参与函数调用时，如果不使用引用参数传递，依然只传递值（当然，这个值可能是引用类型）。这是因为 **C#** 对所有引用的使用都会自动解引用。

### 可选参数和命名参数

值参数可以使用可选参数特性，即提供一个默认值在调用时可以省略，对于引用类型则只能使用 `null` 作为默认值。比较奇怪的是 `ref` 不允许使用可选参数（这让我开始怀疑 `ref` 更类似于 **C++** 的引用类型）。此外，可选参数总出现在方法参数列表的最后（但在参数数组之前）。总体上和 **C++** 是一致的。

命名参数是指在调用处通过指定参数名称来提示调用的实际行为：

```c#
class MyProgram {
    public int calculate(int x, int y, int z) {
        return (x + y) * z;
    }
    static void Main() {
        int ans1 = calculate(y: 1, x: 2, z: 3);		// ans = 9
        int ans2 = calculate(z: 0, y: 2, x: 4);		// ans = 0
    }
}
```

**C++** 有生之年很难实现这个功能，因为 **C++** 允许且存在大量的函数声明，并且声明和定义分离也是主流的程序设计思路。此时如果声明和定义处参数名称不同，就不能确定命名参数的标准（最主要，定义是被隐藏的）。并且在已有的各个版本中，参数名称甚至都是可选的。所以命名参数可能没法实现了。

感谢于命名参数，调用存在可选参数的方法时不一定只能从后向前隐藏参数值了。我们可以显式给出特定参数的值，省略的则根据可选参数的默认值来进行调用。这一点非常方便。

## 深入理解类

章节开头，《C#教程》给出了 **C#** 中类中所有可能的成员：

- 数据成员：字段或常量。**C#** 的常量有点类似于 **C++** 的静态编译期常量，即使用 `static constexpr` 修饰的变量。
- 函数成员：方法、属性、构造函数、析构函数、运算符、索引以及事件。这里面除了属性、索引和事件外都是 **C++** 中也存在的概念。

基本所有成员都可以使用访问修饰符和 `static` 修饰符修饰（除了常量和索引器之外）。`static` 修饰的成员（静态成员）可以直接通过类名调用。此外也可以用 `using static` 声明来将静态成员引入当前的命名空间：

```c#
class MyClass {
    public static int val = 10;
}
using static MyClass.val;
class MyProgram {
    static void Main() {
        Console.WriteLine($"MyClass.val = {val}");	// 输出 10
    }
}
```

### 属性

**C#** 的属性看起来就像一个字段。不同的是后面会跟着可选的 `get` 与 `set` 方法：

```c#
class MyClass {
    private int _val;		// 这是实际上的字段
    public int val {		// 并不会分配新的空间
        set {				// set 方法会在为属性 val 赋值的时候调用
            _val = value;	// value 是一个上下文关键字，这里表示输入的参数
        }
        get {				// get 方法会在使用属性 val 的时候调用
            return _val;
        }
    }
}
```

属性可以随意地访问类中其它的字段，所以在实现上相当灵活。同时也可以简化上面这个平凡定义：

```c#
class MyClass {
    public int val { get; set; }	// 实现了默认的属性，这里会分配一个 int 的空间
}
```

当然，这样的情形与声明一个 `public` 字段无异。更多的情况下，我们会通过省略属性的 `get` 和 `set` 控制属性的可读性和可写性；同时也可以为它们加上 `private` 修饰符来控制其对外部的可读性和可写性：

```c#
class MyClass {
 	public int val { private get { return 10} }   // 只提供了对内部的可读性，不可写。
}
```

只读属性的好处在于什么呢？观察下面的例子：

```c#
class RightTriangle {
 	public double A;
    public double B;
    public double Hypotenuse {				// 这里斜边总是取决于另外两边，因此“不可写”，但是其表现得像一个普通的字段
     	get { return Math.sqrt(A*A + B*B) }   
    }
}
```

如果在 **C++** 中，就只能将 `hypotenuse` 设置为一个函数了，这样在调用的时候就显得没有那么好看。我也一直想要让零参数的函数能够省略括号（然后强制函数指针使用取地址运算符就不会有歧义了），可惜这个改变不太可能。我个人对 **C#** 的属性在控制读写的功能上取保守态度，可能是因为习惯了 **C++** 中修饰符成为类型一部分的风格，**C#** 属性的可读写性不能从它的类型中看出来。不过 **IDE** 或许能够告诉我们它实现了怎样的 `get` 和 `set` 方法，这样就没有什么问题。

静态属性没有什么特别的，和静态字段大体相似。

### 构造函数和初始化

**C#** 的构造函数非常类似于 **C++** ，也支持构造函数初始化列表的语法。不过 **C#** 最令人羡慕的是静态构造函数，在任何类实例被创造之前以及静态成员被引用之前就会调用静态构造函数。这样就能保证类中静态成员的初始化以及让其遵守特定的顺序。在 **C++** 的话，做到同样效果就是一个非常消耗脑细胞的操作（我寻思加一个静态构造函数问题应该不大吧）。

**C#** 中的初始化和对象构造是一个并列的概念，总是在构造对象之后才进行初始化：

```c#
class MyClass {
 	public int x;
    public int y;
    private int z = 5;
    public MyClass(int z_) : z(z_) {}
}
class MyProgram {
 	static void Main() {
     	MyClass mc = new MyClass { x = 10, y = 20 };	// x = 10, y = 20, z = 5
        MyClass mc = new MyClass(5) { x = 10, y = 20 };	// x = 10, y = 20, z = 5
    }
}
```

这是一个比较方便的语法糖，可以通过初始化语法将指定的公有成员进行赋值。

至于 **C++** 的初始化？别问我 **C++** 的初始化，它简直像一坨屎。从功能上当然是不缺的，但是它就是一坨屎。**C++20** 开始支持的聚合初始化有类似于上文中初始化的格式，不过要求巨多（你可以基本认为只有写成 **C** 结构体那样的才能使用这样的初始化语法）。

### `this` 关键字

**C#** 中的 `this` 除了能表示当前实例的引用外，还能用于定义索引器。

```c#
class MyClass {
    private string str1;
    private string str2;
    private string str3;
    public string this [int idx] {	// 索引器使用 this 关键字以及 [] 符号，初看有些奇怪但是不无道理
        get {
            switch (idx) {
                case 0: return str1;
                case 1: return str2;
                case 2: return str3;
                default: throw new ArgumentOutOfRangeException("Index");
            }
        }
        set {
            switch (idx) {
                case 0: str1 = value;
                case 1: str2 = value;
                case 2: str3 = value;
                default: throw new ArgumentOutOfRangeException("Index");
            }
        }
    }
}
class MyProgram {
    static void Main() {
        MyClass mc = new MyClass();
        mc[0] = "abc";
        mc[1] = "def";
        mc[2] = "ghi";
        Console.WriteLine("mc.str1 = {mc[0]}, mc.str2 = {mc[1]}, mc.str = {mc[2]}");
    }
}
```

索引器本身也是一个方法（或者说是属性），因此可以被重载。这就给 **C#** 增加了不少表达力。
