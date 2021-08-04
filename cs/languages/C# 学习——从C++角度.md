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
     	sum = x + y   
    }
}
```



