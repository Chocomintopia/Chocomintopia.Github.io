# 目录
- [编译](#编译)
- [链接](#链接)
- [C++ 源代码编译生成可执行文件过程](#c-源代码编译生成可执行文件过程)
- [变量](#变量)
- [函数](#函数)
- [头文件](#头文件)
- [Debug](#debug)
- [Visual Studio设置](#visual-studio设置)
- [if 语句](#if-语句)
- [循环](#循环)
- [控制流语句](#控制流语句)
- [指针](#指针)
- [引用](#引用)
- [类 class / struct](#类-class-struct)
- [静态 Static](#静态-static)
  - [类或结构体外部的static](#类或结构体外部的static)
  - [类或结构体中的static](#类或结构体中的static)
  - [局部static](#局部static)
- [枚举 enum](#枚举-enum)
- [构造函数](#构造函数)
- [析构函数](#析构函数)
- [继承](#继承)
- [虚函数](#虚函数)
- [接口/纯虚函数 interface](#接口纯虚函数-interface)
- [可见性](#可见性)
- [数组](#数组)
- [字符串](#字符串)
- [CONST](#const)
- [构造函数初始化列表](#构造函数初始化列表)
- [三元操作符](#三元操作符)
- [创建并初始化C++对象](#创建并初始化c对象)
- [new](#new)
- [隐式构造函数 隐式转换](#隐式构造函数-隐式转换)
- [运算符重载](#运算符重载)
- [this](#this)
- [栈作用域生存期](#栈作用域生存期)
- [智能指针](#智能指针)
- [拷贝与拷贝构造函数](#拷贝与拷贝构造函数)
- [-> 箭头操作符](#-箭头操作符)
- [vector](#vector)
- [std::vector使用优化](#stdvector使用优化)
- [C++库](#c库)
  - [静态链接](#静态链接)
  - [动态链接](#动态链接)
  - [创建库和使用库](#创建库和使用库)
- [多返回值](#多返回值)
- [模板 template](#模板-template)
- [堆与栈](#堆与栈)
- [宏](#宏)
- [auto](#auto)
- [std::array](#stdarray)
- [函数指针](#函数指针)
  - [回调](#回调)
- [labmda](#labmda)
- [生成文件列表](#生成文件列表)
- [本文目录使用python脚本自动生成](#本文目录使用python脚本自动生成)

------

我们有很多尚未解决的问题 在那些文本里我使用了 *暂时* 这个词语 请使用网页搜索功能

只有main函数可以没有返回值 默认返回0 其他函数都要有返回值 或者void

`cout << “Hello World” << endl`
实际上这个 << 是重载运算符 可以认为是个函数
是把hello world推到cout流中 然后在终端输出 endl是换行

# 编译

c++并不关心你的文件 文件只是提供给编译器源代码的一种方式 你负责告诉编译器 你输入的是什么类型的文件 以及编译器应该如何处理它

**头文件实际上是编译器预处理的 事先复制到了cpp文件中 于是头文件就和cpp一起被编译了 每个cpp文件都被编译成了object file(.obj)目标文件 然后用link粘合起来就是exe可执行文件 编译一个cpp就是obj 编译整个项目就是exe**

`#include` 实际上就是预处理器打开这个头文件 阅读它的所有内容 然后把它粘贴到你写的内容里 所以你可以写自己的头文件 命名为什么什么.h

```c++
//EndBrace.h 自己写的头文件 内容只有一个}右括号
}
```

```c++
//function.cpp
int function()
{
	//随便写点什么代码
#include "EndBrace.h" //这里就可以用这个include来代替这里缺的}
```

这就是预处理器做的 你如果写了一个`#define INTEGER int` 那么预处理器就会把你代码里所有的INTERGER替换成int 预处理完其实是得到一个 `.i` 文件
```c++
//function.i
int function()
{
	///随便写点什么代码
}
```

这就是 `.i` 文件里的样子 不过会比这个多一些编译器自动生成的注释

`#if` 让我们包含或者排排除基于给定条件的代码

```c++
#if 1
int multiply(int a, int b)
{
    int result = a * b;
    return result;
}
#end if
```

在 `.i` 文件里就是

```c++
int multiply(int a, int b)
{
    int result = a * b;
    return result;
}
```

如果是

```c++
#if 0
int multiply(int a, int b)
{
    int result = a * b;
    return result;
}
#end if
```

`.i` 文件里就什么都没有 这段就是被禁用的代码

Visual Studio中 对于当前项目 选择 属性—C/C++—预处理器—预处理到文件 选择是 同时上方配置那里从 活动(Debug) 改成 所有配置 再去build（生成）就可以得到 .i 文件

 .obj 文件里全都是二进制 也就是机器代码

<span id="mypoint_1"></span>属性—C/C++—输出文件—汇编程序输出 原本这里应该是无列表 选择仅有程序集的列表/FA 再build 那么就可以得到一个 .asm 文件（[另一种查看汇编的方案](#mypoint_2)） 这个就是汇编语言 不再是机器代码了 而是汇编语言 这是CPU将要执行的实际指令 可以看到函数名字前面有一堆看似乱码的东西 这是函数的签名 可以唯一地定义你的函数 如果我们有多个obj 函数也被定义在多个obj中 链接的工作就是把所有的函数链接在一起

我们也可以从汇编文件中看到 变量设置得很多的话 实际上是影响效率 比如你可以直接返回 a+b 而不是再设置一个变量 c=a+b 再返回 c 这样的话会多出来很多针对于变量的 mov 指令

<span id="mypoint_5"></span>debug模式下也不会给你做优化 在属性—C/C++—优化—优化 原本应该是已禁用/Od 选择最大优化/O2 同时上方配置那里从 所有配置 改成 Debug 然后再build 它就会报错 告诉你O2和RTC不兼容 现在我们要继续去 属性—C/C++—代码生成—基本运行时检查 原本应该是两者(/RTC1，等同于 /RTCsu) (/RTC1) 在这里选择默认值 就不会再执行运行时检查 再看 .asm 汇编文件 就会发现 文件变得小多了 比如减少了一些针对变量的 mov 指令

如果我们只写 return 5\*2 不开启优化 会发现 汇编文件里 只有 `mov eax, 10` 而没有5\*2 这叫常数折叠 只要是常数就都直接算 没有指令

这就是编译 这是没有链接之前做的事 其实就是预处理之后得到 .i 文件 .i文件里是机器指令 它同时也可以用另一种表达方式（汇编语言）

如果只编译 就不会链接

如果build（生成）或者执行（按F5） 就会发生链接

# 链接

错误列表 C开头的错误代码 就是编译错误 LNK开头的错误代码 是链接错误

实际上程序的入口点并不一定是main函数 也可以在属性—链接器—高级—入口点 进行配置

“未解决的外部符号”报错 就是链接器找不到它需要的东西

<span id="mypoint_6"></span>如果在函数前面加一个 [static](#mypoint_7) 就说明这个函数只在当前cpp文件里会被使用 其它cpp文件里都不会用到 那么它就不用参与链接 其他cpp文件就不使用 

参数不对 返回类型不对 函数名不对 都会发生链接错误

函数或者变量 有相同的名字和相同的签名 也会发生链接错误

比如你写了一个头文件Log.h 在里面定义了一个函数 然后在两个cpp里都调用这个头文件 实际上就是把这个头文件复制到了两个cpp文件里 那么就是两个cpp文件里都写了这个函数的定义 定义重复了 如果两个cpp里都调用了头文件里的同一个函数 就会报链接错误 “未解决的外部符号”

解决方案：

1. 可以把这个函数定义为static `static void Log(const char* message)`这样这个函数被复制过去之后 就只在cpp文件内部生效 内部函数对于其他obj文件不可见 不会参与链接

2. 也可以把这个函数前面加上inline 意思是将函数调用替换为函数体 也就是比如 定义了函数体 `std::cout << message << std::endl;` 函数名为 `inline void Log(const char* message)` 这样实际上调用 `Log("Initialized Log");` 就等于是替换成了`std::cout << "Initialized Log" << std::endl;` 而并不复制函数到达cpp文件里 只要函数体

3. **（最佳）**把这个Log函数 不再写在Log.h里 而是写在Log.cpp里 Log.cpp被称为翻译单元 然后在Log.h里只保留Log函数的声明 `void Log(const char* message);` 不用static 也不用inline 这样链接之后 其他cpp文件仍然可以调用Log函数 但并不会重复 就不会链接错误

# C++ 源代码编译生成可执行文件过程

1. 预处理 【.cpp .h .hpp 到 .i】
   1. #include 将头文件复制到源文件
   2. 处理宏定义#define 和 条件编译#ifdef、#endif
   3. 删除注释 添加行号和文件名标识（用于调试）

2. 编译 【.i 到 .s】
   1. 将预处理后的代码 转换为平台相关的**汇编代码** 人类可读
   2. 进行语法和语义检查 生成低级中间表示

3. 汇编 【.s 到 .o(Unix-like)/.obj(Windows)】
   1. 将汇编代码转换为**机器码** 生成二进制object file
   2. 目标文件包含代码段（机器指令） 数据段（全局变量） 符号表（函数/变量引用）

4. 链接 【.o/.obj .a .lib .so .dll 到 .exe(Windows)/无扩展名的可执行文件(Unix-like)】
   1. 合并所有目标文件和库 解析符号引用（如函数调用）
   2. 分配内存地址 生成最终可执行的二进制文件
   3. 处理静态库（代码直接嵌入） 动态库（运行时加载）

# 变量

int 4个字节byte 32位有符号 有一位表示符号 其余31位表示实际的数字 2^31^ 20多亿 这是正数的范围 但我们还需要表示负数和0 如果是无符号数unsigned 那就是从0到 2^32^ 

char 1个字节 short 2个字节 long 4个字节 long long 8个字节 但是到底几个字节都取决于编译器 我们可以调用`sizeof(long)` `sizeof(long long)`去查询 或者写`sizeof long`也行 这些数据类型也都可以变成unsigned 

char可以表示数字 也可以表示字符 这不是说其他整数类型不能表示字符 实际上字符也只是一个数字 根据ASCII码对应 但是根据编程习惯 我们一般期待char是一个字符 而其他整数类型代表的就应该是数字 `char a=65` 用cout对a进行输出 我们会得到字母A `char a='A'` 也是会输出A 因为cout就是会把变量a看成是一个字符 如果是`short a=65` 就会cout输出数字65 `short a='A'` 还是会cout输出65 **数据类型之间唯一的区别 就是分配多少内存的区别**

float 4个字节 double 8个字节 `float virable=5.5` 你以为你定义了一个float 实际上你定义了一个double `float virable=5.5f` 这才是真的float 或者`float virable=5.5F` 

<span id="mypoint_3"></span>bool true或者false 但是如果`bool virable=true` cout之后会输出数字1 因为实际上计算机不知道什么true还是flase 它只知道0和1 **0表示flase 任何不是0的数字都是1** 计算机只会处理数字 bool是1个字节 我们有巨大的1byte内存地址空间用来放1个bool值 我们不一定要确定是哪个bit被设置为1 只要这个byte里有东西 不为0 那它就是true 所以true有可能是1 但并不强迫我们设置为1 [关于在C++中 bool 非0为true 0为false的讨论](#mypoint_4)

但为什么bool不是1个bit 它确实是只用1bit 但当我们处理寻址内存时 我们没有办法寻址只有1个bit位的内容 我们只能访问字节 但你也可以在1byte内存里存储8个bool数 但仍然是分配1个字节的内存

可以用这些基本数据类型 写我们自己的自定义数据类型

# 函数

就是代码块 在class类里面 叫做方法 谈到函数时 我们明确地指不属于类里面的东西 你可以认为函数是有一个输入 也有一个输出 我们可以为函数提供一定的参数 当然也可以不提供参数 函数也可以不返回任何东西 就是void **函数是为了防止写重复代码的** 但也不用所有的东西都写成函数 会让程序变慢 每次我们调用函数时 编译器生成一个call指令 就会进入堆栈结构 把像参数这样的东西推进堆栈 还会需要一个返回地址 又会jump到二进制执行文件的不同位置 以便执行我们的函数指令 为了将push进去的结果返回 又要回到最初调用函数之前 就像在内存中跳跃来执行函数 跳跃和执行都需要时间 这些都是因为编译器决定保持我们的函数作为一个实际的函数 并不做内联inline 

# 头文件

头文件的作用不仅仅是写一些声明 然后在多个cpp文件中使用 如果我们在一个文件中创建函数并且想在另一个文件中使用 但C++并不知道这个函数的存在 于是我们需要用一个公共的地方只存放声明（因为我们只能定义函数一次） 只有一个声明没有函数体 只是说这里有一个函数是存在的

比如我们实际上在某个cpp文件中使用了另一个cpp文件的函数 如果不声明 编译这个cpp文件时就会报错 所以我们要在这个cpp文件中添加那个函数的声明 这样才能通过编译 最后build的时候就能链接 正确找到那个函数 但是如果每一个cpp文件都要用这个函数 就要到处复制粘贴很麻烦 我们需要创建头文件 #include指令有复制和粘贴的功能 把声明都写到头文件里吧

`#pragma once`  创建.h头文件时为我们自动生成了这一句 所有以#开头的 都是预处理器命令或者预处理器指令 这意味着它将被优先处理 `#pragma once`意思是只包括这个文件一次 负责监督这个头文件 防止单个头文件多次被包含 并转换为单个翻译单元 这并不妨碍我们将头文件放到程序的多个位置 只是说放在一个翻译单元、一个cpp文件 原因是 如果我们不小心多次包含了一个文件并转换成一个翻译单元 我们会得到duplicate复制错误 因为我们会复制粘贴整个头文件多次 比如我们在头文件里写了一个结构体 如果我们放弃了pragma once 那么只要调用一次头文件就会复制一个结构体 最后我们会在同一个文件里有很多个相同名字的结构体 但你可能说我们并不会愚蠢到在同一个文件里多次使用同一个头文件 但是**头文件有嵌套问题 可能你在创造一个头文件时使用了另一个头文件的内容 会创造一链条的头文件**

如果不用pragma once 就用`#ifndef` 这是一种过去的方式 可能有人会用 你不要用
**现在就用pragma once**

```c++
//这是在头文件里写的
#ifndef _LOG_H //这是初始检查 检查是否有一个_LOG_H的符号被定义了 如果没有被定义 就继续 在编译中就包含下列代码 如果已经被定义了 下面这些直到#endif之前的东西就不会被包含进来 就被禁用了
#define _LOG_H

//一些头文件里的东西

#endif
```

`#include "Log.h"` `#include <iostream>` 有些用" " 有些用< > 我们[暂时](#mypoint_12)不讨论

iostream也是一个文件 它只是没有扩展名 C++的设计者为了将C++标准库与C标准库进行区分才这样做 C标准库常常有.h扩展 但C++没有 可以在#include iostream那句话右键 转到文档iostream来查看代码 在文档标签页那里右键 就可以打开它所在的文件夹 或者复制它所在路径

# Debug

断点 和 读取内存

计算机总是对的 它报错的话 99.99%是你错了而不是它错了

在代码的任何一行我们都可以设置断点 当程序进行到这一行时 就暂停 在整个项目中 它会挂起执行线程 我们暂停程序 然后看看它的内存中发生了什么 一个运行中的程序所需的内存是相当大的 包括你设置的每一个变量 包括要调用的函数 当你将程序中断后 内存数据实际上还在 能查看内存 对于诊断程序出问题的原因非常有用 通过查看内存可以看到每一个变量的值 这个变量不应该设置为这个值 肯定出错了 还可以单步逐行运行代码 

设置断点时 要处于debug模式 而不是release模式 想运行到哪一行停止就在哪里设置断点 然后点击本地windows调试器

step into 逐语句(F11) 意思是进入到这行代码的函数里面
step over 逐过程(F10) 意思是从当前函数跳到下一行代码
step out 跳出(Shift+F11)意思是跳出当前函数 回到调用这个函数的位置

```c++
int main()
{
	Log("Hello World!");
	std::cin.get();
}
```

我们在第二行设置断点 然后step into 就会进入Log.cpp（Log函数所在的文件） 里的Log函数

```c++
void Log(const char* message)
{
	std::cout << message << std::endl;
}
```

把鼠标悬停在 `Log(const char* message)` 的message上面 可以看到 0x00f29b30 "Hello World!" 只是设置了函数栈帧结构 message已经被设置成了Hello World!
然后我们step over 黄色箭头就跳到了Log函数的第二行 箭头的意思是这一句还没执行 但将要执行这一句了 现在Hello world还没有被打印出来 如果我们再按一次step over 就会发现Hello world已经被打印出来了 因为我们调用了std::cout 再step over 我们就回到了main函数 再按step over 黄色箭头就到达了main函数的第3行 再step over 在弹出窗口按enter 调试就结束了

再来一个例子

```c++
int main()
{
	int a = 8;
	a++;
	const char* string = "Hello";

	for (int i = 0; i < 5; i++)
	{
		const char c = string[i];
		std::cout << c << std::endl;
	}
    
    Log("Hello World!");
	std::cin.get();
}
```

在第2行设置断点 然后调试 鼠标悬停到 `int a = 8;` 那句的a上面 可以看到a -858993460 注意黄色箭头现在在第2行 意味着我们还没有运行第二行代码 变量 a 的值为 -858993460 这个值通常表示未初始化的局部变量在调试模式下的默认值 这只是未初始化的内存 我们也可以在自动窗口 看到

| 名称 |     值     | 类型 |
| :--: | :--------: | :--: |
|  a   | -858993460 | int  |

局部变量窗口

|  名称  |                值                |     类型     |
| :----: | :------------------------------: | :----------: |
|   a    |            -858993460            |     int      |
| string | 0xcccccccc <读取字符串字符时出错 | const char * |

这个值表示一个未初始化的指针 通常在调试模式下 未初始化的指针会被设置为 0xcccccccc 以便于识别

监视1

|      名称      |  值  | 类型 |
| :------------: | :--: | :--: |
| 添加要监视的项 |      |      |

在 添加要监视的项 这里 可以输入想要监视的变量 比如输入a 然后按enter 也可以输入string 最后监视1窗口就变成这样

|  名称  |                              值                              |     类型     |
| :----: | :----------------------------------------------------------: | :----------: |
|   a    | -858993460<br />（可以在这里右键 让它16进制显示 就是0xcccccccc） |     int      |
| string |               0xcccccccc <读取字符串字符时出错               | const char * |

在菜单栏点开 调试—窗口—内存—内存1 就可以看到一个窗口
在地址 0x001452C0那里 输入&a 再按enter 就可以得到变量a的内存地址 此刻也就是0x010FFDE8 可以看到一堆cc cc cc cc cc cc cc cc 实际上是16进制 windows自带计算器 利用程序员模式 HEX那里是输入16进制 cc 转换成10进制DEC是204 为什么是一堆cccccc很有规律的样子 内存不应该是随机的吗？ debug会让我们的程序变慢 因为编译器会让我们的程序做某些额外的事情让调试更轻松 这个内存是一堆ccccccc 意思是它是未初始化的栈内存 如果我们debug的时候 出现问题 可以看看内存 如果是一堆ccccc 说明没有初始化变量 release模式下不会这样

监视1的 a的值变成了8 也可以看到内存里&a 变成08 00 00 00 cc cc cc cc 可以看到4个字节的内存已经设置为8 这里面2个数字代表1个字节 所以用16进制 每两个16进制数与1个byte对齐 所以08 00 00 00就是4字节 32位 1个int

再step over 可以看到a变成了9 再step over string被初始化了 string 的值是 0x00149B30 "Hello" 还告诉了我们这个字符串的内存地址 我们在内存里查询0x00149B30这个地址 会看到48 65 6c 6c 6f 00 00 00 每2个数字是1个字节 1个char 1个字符 是ASCII码 48 65 6c 6c 6f 分别对应字母 H e l l o 但我们在内存1窗口的右侧 可以看到 Hello............?...?..h?..??..??...?..........................Stack around the variable '.' was corrupted.....The variable '..' is being used without being initialized... 后面这些话在release模式下不会存在

再step over 我们就进入了for循环 可以看到i变成了0 类型int 再step over c是72 'H' 再step over 字母H被打印到了控制台 再step over 黄色箭头到了for循环的 `}` 那行 再step over 黄色箭头到了for循环的 `{` 那行 会进行一个i与5的比较 然后i+1 再step over 可以看到i变成了1 再step over c变成了101 'e' 我们在内存1窗口输入&c 就可以在窗口右侧看到随着循环的进行 c的字符的变化

你不想一直一直按step over直到结束for循环 当然我们可以按**step out 但是那是跳出整个函数** 在本例中也就是跳出了main函数 它会一口气把main函数执行完毕 会按行分别打印H e l l o 最后一行打印Hello world! 

我们可以在希望它停止的地方再设置一个断点 比如本例就是在第11行再设置一个断点 然后按工具栏的继续按钮 它会直接一直运行直到遇到下一个断点 本例中黄色箭头就直接跳到第11行 也可以看到控制台已经逐行打印了H e l l o

我们的内存窗口还一直停留在查看&c的模式 发现即使已经跳出for循环了 参数c的所在的那部分内存仍然活跃 仍然是字母o Hello的最后一个字母 暂时我们先不思考这个问题

再按step out 就打印出了Hello world! 我们现在将要执行 `std::cin.get();` 即使我们在控制台按enter 也什么都不会发生 如果我们按工具栏的继续按钮 整个调试就停止了 

一个程序 就是由内存构成的 内存是最重要的

[利用汇编debug](#mypoint_2)

# Visual Studio设置

Visual Studio默认安装的是MSVC编译器（微软的C++编译器） 而非g++

MSYS2是一个在Windows上提供类Unix开发环境的工具集 包含：
包管理器pacman 方便安装开发工具 如 g++、make
MinGW-w64工具链 提供 Windows原生可执行的gcc编译器 生成 .exe
Unix 工具 如bash、git、ssh
常用于需要gcc工具链的项目 如跨平台开源库编译
MSYS2 不是 Python 虚拟环境（比如anaconda3） 而是一个开发工具链环境

如果你单独安装过MinGW或通过MSYS2安装，路径可能是：
C:\msys64\mingw64\bin\g++.exe
C:\MinGW\bin\g++.exe

| **场景**             | **推荐工具链**        | **优点**                       |
| :------------------- | :-------------------- | :----------------------------- |
| Windows 原生开发     | Visual Studio MSVC    | 深度集成 IDE，调试方便         |
| 跨平台项目（需 GCC） | MSYS2 + MinGW         | 兼容 Linux 代码，方便移植      |
| 快速管理第三方库     | vcpkg + Visual Studio | 自动处理依赖，无需手动配置路径 |

创建项目的时候 不要勾选将解决方案和项目放在同一个目录中 创建之后我们得到

```
├──project_test
|  ├───project_test.sln
|  ├───project_test                 # 文件夹
|  |   ├───Project_test.vcxproj     # 实际上是XML文件
|  |   ├───Project_test.vcxproj.filters
|  |   ├───Project_test.vcxproj.user
```

我们可以看到那个解决方案(.sln)是和项目同名的 如果我们只有一个项目就没什么问题 

然后在解决方案里

```
├──project_test
|  ├───引用
|  ├───外部依赖项
|  ├───头文件
|  ├───源文件
|  ├───资源文件
```

这并不是文件夹 这是filters 不是文件夹 在project_test项目那里右键—添加 没有添加文件夹 只有添加筛选器(filters) 我们下面把filters重新翻译为过滤器 如果我们添加一个过滤器 磁盘上看起来不会发生任何改变 只有那个Project_test.vcxproj.filters文件 包含了我们创建的这类虚拟文件夹 这些过滤器组织了我们的源代码 但在磁盘上却不存在 但它们确实存在于这个解决方案资源管理器视图里 如果我们在源文件—添加—新建项 创建一个cpp文件 会发现它就和那些`Project_test.vcxproj` `Project_test.vcxproj.filters` `Project_test.vcxproj.user` 在同一个文件夹里 这太混乱了 所以我们还是创建一个名为`source`或者`src`的文件夹 在其中存放所有源代码 头文件一类的东西

解决方案资源管理器上方有个工具栏 有一个 显示所有文件 的按钮 这样视图就会变成硬盘里的目录结构 这时候再在project_test项目那里右键—添加 就有添加文件夹的选项 可以在此时创建`src`文件夹 把我们新建的cpp文件移动到src文件夹中 切换视图 发现我们新建的cpp仍然在源文件里 无论把它放到哪里 都不会影响真实的文件组织形式

> 这些东西当时在 GAMES104 打开Piccolo引擎源码时感到不解 查询DeepSeek得到 但这次才算深刻理解文件组织形式

我们快速写一个hello world程序 然后对整个项目进行生成 在输出窗口可以看到
```
生成开始于 0:54...
1>------ 已启动生成: 项目: Project_test, 配置: Debug x64 ------
1>Project_test.vcxproj -> D:\coding\C++\Project_test\x64\Debug\Project_test.exe
```

Debug x64 是因为我们在Debug x64模式下进行的生成
x64是64位 win64
x86是32位 win32

我们现在知道了exe文件所在位置 但是真正打开这个文件夹 却找不到Project_test.exe文件 因为你并没有仔细看 实际上你打开的是 D:\coding\C++**\Project_test\Project_test**\x64\Debug

```
├──project_test
|  ├───project_test.sln
|  ├───Debug            # 要打开这个 才可以看到exe文件
|  ├───x64
|  ├───project_test     # 文件夹
|  |   ├───src          # 文件夹 存放源代码
|  |   ├───x64          # 文件夹
|  |   |   ├───Debug    # 文件夹 你刚才打开的就是这个 里面并没有exe文件
```

这真的很难找到exe文件

我们在project_test项目那里右键—属性 首先把配置—活动(Debug) 改成 所有配置 活动(x64)也改成 所有平台

把输出目录改成 `$(SolutionDir)bin\$(Platform)\$(Configuration)\` 把它放在解决方案目录下面 也就是根目录 本例中就是Project_test 这样如果我们有多个项目 比如我们构建主应用程序需要的dll文件 我们希望它们都在同一个文件夹中 不想再每个项目文件夹里面去处理这些输出文件 只是想把我所有构建的二进制文件放在同一个地方 `bin`的意思就是二进制 然后在合适的platform文件夹下 本例中就是x64 也可以是win32 然后在configuration下 本例中的配置是Debug 也可以是release

中间目录 改成 `$(SolutionDir)bin\intermediates\$(Platform)\$(Configuration)\` 就只是在bin下面多了一个intermediates

然后点确认 我们在project_test项目那里右键—清理 删除许多旧文件 这样删除不彻底 还是手动去文件资源管理器把有debug和x64的文件夹都删除 重新build 现在exe文件就在 `D:\coding\C++\Project_test\bin\x64\Debug` 这个文件夹里同时还有`Project_test.pdb`和`Project_test.ilk` 许多中间文件都在`D:\coding\C++\Project_test\bin\intermediates\x64\Debug`

在project_test项目那里右键—属性-常规 输出目录那里 在编辑的时候 最右侧有一个选项符号 展开 点击 <编辑...> 然后点击 宏>> 我们就可以看到很多 \$( ) 这种形式的东西 在上方空白方框里 搜索SolutionDir 可以看到在本例中的目录为 `D:\coding\C++\Project_test\` 在最后它是自带 \ 的 所以我们在设置输出目录和中间目录时 \$(SolutionDir) 与 bin 中间 不用写 \

# if 语句

如果条件为真 我们跳到源代码的某一部分 如果值为假 我们跳到我们源代码的另一部分 我们这里说是源代码 但在实际运行的应用程序中是指机器指令 当我们开始一个应用程序时 整个应用程序及其所有模块加载到内存中 所有这些指令组成了我们的程序 现在都存储在内存中 当我们有了条件语句所产生的分支 我们是在告诉电脑跳到我们的这部分内存 在那里开始执行我们的指令 if语句和分支通常有比较大的开销 如果效率高做优化就避免写if语句

```c++
int x = 6;
bool comparisonResult = (x == 5);
if (comparisonResult == true)
	Log("Hello, World!");

std::cin.get();
```

`bool comparisonResult = (x == 5);` 这里的`==`是在C++标准库中被重载了 相当于写一个函数 接受两个整数参数 然后检查这两个整数的内存 实际上是在获取它们4个字节的内存 比较每个字节 为了让这两个整数是相等的 内存的每一位都必须相同 看它们是否相等 相等就返回true 

`if (comparisonResult == true)`和`if (comparisonResult)` 是同一个意思

<span id="mypoint_2"></span>在debug中 右键某一行代码—转到反汇编 就可以查看它的汇编指令 不再需要在输出文件里修改成[.asm文件输出](#mypoint_1) 源码无法找到错误原因时 可以求助于调试CPU指令

```c++
	int x = 6;
00007FF68B39240C  mov         dword ptr [x],6  
```

将值6 move到这个寄存器 就是变量x被设置为6

```c++
	bool comparisonResult = (x == 5);
00007FF68B392413  cmp         dword ptr [x],5  
00007FF68B392417  jne         main+35h (07FF68B392425h)  
00007FF68B392419  mov         dword ptr [rbp+0F4h],1  
00007FF68B392423  jmp         main+3Fh (07FF68B39242Fh)  
00007FF68B392425  mov         dword ptr [rbp+0F4h],0  
00007FF68B39242F  movzx       eax,byte ptr [rbp+0F4h]  
00007FF68B392436  mov         byte ptr [comparisonResult],al  
```

把5加载到同一个寄存器 然后jne（就是jump not equal  而je就是jump equal jne和je都不是普通的跳转语句jmp 它是条件跳转语句） 现在就是比较5和6这两个值 如果不相等 not euqal 就跳转到内存地址07FF68B392425h 实际上就是`00007FF68B392425  mov         dword ptr [rbp+0F4h],0 `这一行 现在我们已经知道5和6不相等 在debug时jump over 就会发现黄色箭头确实会到这一行 所以这一行就是将0移动到这个寄存器 这个寄存器是rbp这个实际的寄存器（rbp/ebp 基址寄存器 用于地址指定） 加上一定的偏移量 实际上我们知道它是把0移动到了bool值那里 bool值就被设置成了false 最后两行那个movzx mov 我们就不关心了

```c++
	if (comparisonResult == true)
00007FF68B392439  movzx       eax,byte ptr [comparisonResult]  
00007FF68B39243D  cmp         eax,1  
00007FF68B392440  jne         main+5Fh (07FF68B39244Fh)  
```

将某些值加载到eax寄存器（通用寄存器）中 仍然是cmp然后jne comparisonResult不为true 不为1 not equal 就跳转07FF68B39244Fh 是`std::cin.get();`那一行 跳过了Log函数 如果这里equal了就是直接继续Log函数

<span id="mypoint_4"></span>但实际上我们[复习bool](#mypoint_3)又知道 true不一定为1 只要非0就是true 在这里为什么是eax里的值一定要与1比较呢？ 以下为DeepSeek回复

1. 类型提升规则
   当bool参与比较或运算时 会隐式转换为int类型 true提升为1，false提升为0 则`comparisonResult == true`等价于`(int)comparisonResult == 1` 编译器直接生成与1比较的指令

2. 编译器对bool的合法性假设
   编译器假设程序遵循C++标准 所有bool变量只能存储0或1 若通过非法手段（如内存覆写）使bool值为其他非0数 属于未定义行为 编译器无需处理

3. 逻辑操作的结果规范化
   逻辑运算符（如`==`、`&&`）生成的bool值会被规范化为0或1

   ```c++
   int a = 5, b = 3;
   bool c = (a == b); // c = 0（false）
   bool d = (a || b); // d = 1（true）
   ```
   因此 直接比较1是安全的

4. 优化与效率
   直接比较eax是否为1（单条cmp指令）比检查非0（需两次操作 测试是否为0 然后取反）更高效 编译器在合法代码前提下选择最优路径

其实如果那句修改成`if (comparisonResult)` 就不会涉及eax与1的比较 会变成

```c++
	if (comparisonResult)
00007FF6C0CD2439  movzx       eax,byte ptr [comparisonResult]  
00007FF6C0CD243D  test        eax,eax  
00007FF6C0CD243F  je          main+5Eh (07FF6C0CD244Eh)  
```

你不需要考虑它是不是true 是不是1 只需要考虑它是不是0
`test eax, eax`等效于`cmp eax, 0` 但 test指令更高效 test是按位与 cmp是做减法 如果为0 就je

当然我们知道debug模式下是不会做任何优化的 实际上仅就5和6比较那里 完全可以做常数折叠 编译器自己就能做到 不需要在程序运行的时候再做比较 

[开O2优化](#mypoint_5)之后 发现debug模式下右键已经不能反汇编了 只能[.asm输出](#mypoint_1) 关于这个汇编文件 仍然是询问了DeepSeek

主函数中有一个条件判断 当x等于5时调用Log函数 但用户将x初始化为6 所以比较结果应该是false 不会执行Log调用 在未优化的反汇编中 确实进行了比较和跳转 而优化后的汇编代码中 这些步骤被省略了 直接调用了std::cin.get()

优化后的.asm文件中 main函数部分非常简短 只调用了__CheckForDebuggerJustMyCode 然后调用了cin.get() 没有条件判断和Log相关的代码 这说明编译器在优化过程中识别到条件永远不会满足 因此完全移除了相关的代码

未优化的反汇编代码中 可以看到x被赋值为6 然后进行比较 设置comparisonResult为false 跳过了Log调用 而开启O2后 编译器进行了常量传播和死代码消除 因为x是常量6 比较x==5的结果必然是false 所以整个if语句块都会被移除 包括Log调用 因此优化后的代码不再包含这些无效的代码路径 直接执行cin.get()并返回

在开启O2优化后 编译器通过以下关键优化步骤彻底移除了条件判断和Log调用：

1. 常量传播 (Constant Propagation)
   1. `int x = 6`被识别为编译期常量
   2. 所有使用`x`的地方直接替换为6
2. 死代码消除 (Dead Code Elimination)
   1. 由于`x == 5`被替换为`6 == 5`，编译器直接判定结果为`false`
   2. 整个if代码块被识别为不可达代码，包括：
      1. `bool comparisonResult`的初始化
      2. `if (comparisonResult == true)`的条件判断
      3. `Log("Hello, World!")`的调用
3. 函数调用优化
   - 未被调用的`Log`函数被完全移除（假设没有其他调用点）

优化后的等效C++代码：
```cpp
#include <iostream>

int main()
{
    std::cin.get();  // 唯一保留的有效代码
    return 0;
}
```

这种优化属于编译器的最基础优化级别 现代编译器（包括MSVC）在O1/O2级别都会自动进行这类常量传播和死代码消除

`if (comparisonResult)` 这句话做的就是 看看comparisonResult是不是为0 如果不是0 就执行`if{   }`内部的语句 如果写`if(1)` 那么就永远执行内部的语句

其实我们根本不需要存储到变量comparisonResult里 直接写`if (x == 5)` 使用这个变量仅仅是想说明 那个条件实际上是bool类型

如果 if 语句里只有一行 就不需要写 { } 但是不要写在同一行 比如写成 

`````c++
if (x == 5)   Log("Hello World!");
`````

debug到这一行的时候 会搞不清楚正在运行哪里

bool只是数值 而if语句只是对数值进行检查 所以我们还可以写`if (x)` 因为现在x是6 不是0 所以它还是会执行条件满足时的语句

这个技巧在指针中常常使用 如果我们想检验指针是否为空 null 就是0 可以把指针放到一个 if 语句的条件当中

```c++
const char* ptr = "Hello";
if (ptr)
    Log(ptr);
```

因为指针被设置了某个值 它不是null 所以我们成功把这个指针打印到了控制台
如果`const char* ptr = 0;`或者`const char* ptr = nullptr;` 就不会执行`Log(ptr);`
所以写`if (ptr != nullptr)`和`if (ptr)` 效果是一样的

else 和 else if

```c++
if (ptr)
    Log(ptr);
else
	Log("Ptr is null!");
```

```c++
else if ( )
{
	//
}

//实际上等效于
else
{
	if ( )
	{
		//
	}
}

//所以并没有真正的else if 只是将两个语句放在一行而已
//else if并不是C++的关键字 就只是先else 然后if
```

只有在前面的 if 失败后 才会触发 else 语句

我们可以尽量尝试不使用 if 语句或者类似的东西 也就是不用逻辑编程 不是去做一个比较然后通过分支语句来处理 这样做会很慢 要尽量使用数学计算代替

# 循环

游戏循环 只要玩家还没有决定退出游戏 就需要对游戏状态更新 渲染 让角色持续保持移动状态 持续做所有的事情 一帧接一帧地

```c++
for (int i = 0; i < 5; i++)
{
	Log("Hello World!");
}
```

先声明一个变量i 如果条件为真 就跳到 for 循环里 执行循环体内部的代码 当完成了循环体 到达结尾的 } 时 执行 i++ 然后继续检查`i < 5`条件是否为真 最后一步是 i=4 做完循环体 然后 i++ 这之后i为5 `i < 5`条件不再为真 不再进入循环体 跳出循环

for 循环的3段声明
第1段 开始for循环时 运行一次
第2段 bool类型 将在for循环一次结束之后 进行评估
第3段 看上去是要在for循环的最后被运行

但是 我们也可以改成这样 并没有改变程序的行为

```c++
int i = 0;
bool condition = true;
for ( ; condition; )
{
	Log("Hello World!");
	i++;
	if (!(i < 5))
		condition = false;
}
```

`for( ; true; ; )` 或者 `for( ; ; ; )` 这就是无限循环

```c++
int i = 0;
while (i < 5)
{
	Log("Hello World!");
	i++;
}
```

比如我们希望游戏持续循环 只要running变量为true即可一直循环 这种时刻就倾向于用while循环 因为条件是不变的 不需要在每次循环之后改变这个条件 也不需要刻意在循环之前声明这个条件变量 只需要将之前的变量或者函数调用之后的结果拿来用 实际上不需要更新或者初始化某些东西

但当我们处理确定长度的数组时 倾向于使用for循环 因为我们只需要循环某个确定的次数 与此同时 我们跟踪的那个偏移量/索引（比如 i） 可以用于处理数组中的元素

do-while 是无论条件是否满足 先执行循环体一次

# 控制流语句

continue 只能在循环中使用 表示进入这个循环的下一次迭代 如果还有下一次迭代的话 如果没有了 循环就会结束
break 只能在循环中使用 跳出循环 终止循环
return 可以使用在任何地方 直接退出函数

```c++
for (int i = 0; i < 5; i++)
{
    if ((i + 1) % 2 == 0)
        continue;
	Log("Hello World!");
    std::cout << i << std::endl;
}
//Hello World! 变成只在 i 为偶数时输出 i=0 i=2 i=4分别输出
```

```c++
for (int i = 0; i < 5; i++)
{
    if ((i + 1) % 2 == 0)
        break;
	Log("Hello World!");
    std::cout << i << std::endl;
}
//Hello World! 变成只在 i=0 时输出一次 就跳出循环
```

```c++
int main()
{
    for (int i = 0; i < 5; i++)
	{
        if ((i + 1) % 2 == 0)
            return 0;
        Log("Hello World!");
        std::cout << i << std::endl;
    }
    Log("-------");
}
//i=0时直接满足条件 return 不会输出任何东西就结束
//不仅仅是跳出for循环 所以甚至是下面的分割线也没有输出
```

# 指针

对计算机来说 内存就是一切 所有的程序都会被加载到内存中 而指针对于管理和操纵内存非常重要

**指针是一个数字 一个存储内存地址的数字** 内存在计算机里 就像一条线性的街 街上的每座房子都会有地址 这个地址就是1个字节的数据 显然我们需要一种方法来寻址 指针就是这些地址 这些地址告诉我们房子在哪里

**一个指针只是一个地址 它是一个保存内存地址的整数** 忘记所有的类型 类型只是一种为了更便利而产生的虚构 所有类型的指针都只是保存内存地址的整数 

```c++
void* ptr = 0;
```

我们给这个指针的内存地址是0 也就是NULL nullptr 0不是一个有效的地址 我们不能从内存地址0中读取或写入

```c++
void* ptr = NULL;
```

把鼠标悬停在NULL上 就可以看到宏定义`#define NULL 0` NULL 是一个宏定义 通常用于表示空指针 其值为 0

```c++
int var = 8;
void* ptr = &var;
```

在一个已经存在的变量前面加上& 表示取这个变量的内存地址 我们取了变量var的地址 并把它赋值给一个新的变量ptr

调试

| 名称 |               值               | 类型  |
| :--: | :----------------------------: | :---: |
| &var | 0x000000dba079fba4{0x00000008} | int*  |
| ptr  |       0x000000dba079fba4       | void* |
| var  |           0x00000008           |  int  |

我们可以看到 ptr的值为0x000000dba079fba4
只不过是一个64位16进制的数字（2个16进制数字 可以表示8位2进制数字 是1个字节8bit 这里是16个16进制数字 是8字节 也就是64位2进制 “位”这个词语 仅指二进制位bit） 当然我们现在已经知道这个数字的含义就是地址 如果你不知道这一点 那它的值 就仅仅是个数字 我们的编译环境是debug x64 所以无论是哪种类型的指针 它的值都是一个64位的数字 当然针对于&var 因为var是一个int 所以编译器只允许ptr的类型是void\*或者int\* 我们可以把&var强制转换

```c++
double* ptr = (double*)&var;
```

你就发现ptr的值还是一个64位16进制数字
0x000000c58b6ff764 {-9.2559592117432085e+61} 表示一个内存地址0x000000c58b6ff764被解释为指向double类型的指针 解引用后得到的值-9.2559592117432085e+61是无意义的 因为ptr实际指向的是int类型变量var的内存 而不是double
代码中将 &var（类型是int\*）强制转换为double\*导致未定义行为 int和double的内存布局不同 直接转换会导致错误的解释

而变量var是一个32位16进制的数字 符合其作为int的身份 我们把ptr的值 拖拽到内存1窗口的地址栏 可以看到08 00 00 00 说明这个数字 确实是var的地址

```c++
int var = 8;
void* ptr = &var;
*ptr = 10; //会报错
```

`*ptr`是逆向引用指针 dereferencing the pointer 意思是这个指针所指的那个变量 这个地址上所在的那个变量 逆向引用也可以叫做解引用
但如果这个指针的类型是void 那在逆向引用的时候 我们就只知道一个地址 不知道这个变量的类型 就不知道这个变量是多少位多少字节要占多少内存 没办法读写 所以如果想使用逆向引用去对这个变量读取或写入 指针就必须记录变量的类型
本例中变量var是int 所以我们必须告诉编译器 指针ptr指向的变量是一个int 这样才可以对这个地址上的变量进行读写

```c++
int var = 8;
int* ptr = &var;
*ptr = 10;
```

这样我们就成功地将var的值修改为10

```c++
int var = 8;
```

我们像这样创建变量时 就是在栈中创建它

```c++
char* buffer = new char[8];
```

分配了8个字节的内存 并返回一个指向那块内存开始的指针 在内存窗口可以看到 buffer这个地址 确实开辟了8个字节的空间 现在是cd cd cd cd cd cd cd cd 是Visual Studio的调试填充值 表示**未初始化的堆内存** 如果你切换到release模式 可能不会看到这种调试填充值

**未初始化的栈内存** 是cc cc cc cc cc cc cc cc

```c++
memset(buffer, 0, 8);
```

`void *__cdecl memset(void *_Dst, int _Val, size_t _Size) ` 它接收一个指针 这个指针将会是内存块开始的指针 取一个值为0 取一个大小8字节 就将8个字节填入0

如果做`memset(buffer, 'a', 8);` 查看内存1窗口就可以看到 buffer地址上是61 61 61 61 61 61 61 61 

查看内存1窗口就可以看到 以buffer地址开始的8个字节里是61 61 61 61 61 61 61 61 确实是填入了'a'

也可以看到61 61 61 61 61 61 61 61后面有fd fd fd fd 其实在刚才那些cd之后也有fd 这是调试器添加的保护字节 用于检测堆缓冲区溢出 release模式下不会有

上面例子就是使用`new`关键字来申请堆内存 在结束之后也应该删除数据 因为使用了数组来分配堆内存 所以要用`delete[]`

```c++
detele[] buffer;
```

指针本身也是变量 也存储在内存中 所以我们可以做指向指针的指针 二级指针或者三级指针

```c++
char** ptr = &buffer;
```

|  名称   |                    值                     |  类型  |
| :-----: | :---------------------------------------: | :----: |
| buffer  |           0x000002ac05d55070 ""           | char*  |
| &buffer | 0x000000c1deeff728{0x000002ac05d55070 ""} | char** |
|   ptr   | 0x000000c1deeff728{0x000002ac05d55070 ""} | char** |
|  *ptr   |           0x000002ac05d55070 ""           | char*  |

buffer本身就是一个指针 它的值是分配的那块堆内存的起点
&buffer就是指针的指针 它的值是buffer这个指针的地址
ptr=&buffer 它的值也是buffer这个指针的地址
*ptr 是逆向引用 是“buffer这个指针的地址”位置处的变量 也就是buffer这个指针 它的值就是buffer这个指针的值 也就是分配的那块堆内存的起点

0x000002ac05d55070 后面的`""`引号表示buffer这个指针指向的动态分配内存当前存储的是一个空字符串 因为我们前面使用的是`memset(buffer, 0, 8);` 都初始化为0了 如果都初始化为'a' 就应该是`“aaaaaaaa  ”` 8个a 后面还有空格 空格实际上是未定义的内存内容 而不是实际的空格字符 因为buffer 未添加字符串终止符 `memset(buffer, 'a', 8);` 将 buffer 的 8 个字节填充为 'a' 但没有添加 \0（字符串终止符） 因此 buffer 被解释为一个未终止的字符串 读取时会超出分配的8字节范围 访问到未初始化的内存 未初始化的内存是动态分配的内存 可能包含随机值 例如空格或其他字符 这些值在输出时可能被解释为不可见字符或空格

# 引用

引用只是指针的语法糖 引用能做的所有事都可以被指针取代 但尽量去优先使用引用
引用必须要引用已经存在的变量 引用本身并不是新的变量 不占用内存 没有真正的存储空间

```c++
int a = 5;
int& ref = a;
ref = 2;
LOG(a); // #define LOG(x) std::cout << x << std::endl;
```

`int&` 这个&是变量声明的一部分 并不是取地址 现在我们只是为a创造了一个别名ref ref变量是不存在的 它只存在于我们的源代码里 现在我们对ref的任何操作 都是像对a一样

```c++
//整型变量递增函数（无效）
void Increment(int x)
{
    x++;
}
```

```c++
Increment(a);
```

发现a根本没有如我们期望的那样 值递增了1
实际上这个函数只是把a的值 复制给了它新创建的变量value 然后value增加了1
我们需要通过函数真正地修改这个变量

方法1：
用指针把变量a的内存地址传递过去

```c++
void Increment(int* x)
{
    (*x)++;
    //根据运算优先级 如果不加() 就是先算++ 对地址进行递增
    //而我们期待的是先对指针逆向引用 找到这个地址的那个变量的值 对这个值++
}
```

```c++
Increment(&a);
```

我们把a的地址 复制给了函数里的新的指针变量x 再对x逆向引用 就可以直接写入变量a

方法2：
用引用 就是把a复制给了函数里新的引用x x就只是a的别名

```c++
void Increment(int& x)
{
    x++;
}
```

```c++
Increment(a);
```

一旦声明了引用 就不能改变它引用的东西

```c++
int a = 5;
int b = 8;

int& ref = a;
ref = b;
//此时 a=8, b=8
```

并不是如我们所计划的那样 ref去变成引用b 而是a的值被赋予为b的值
所以在声明引用的时候 就要为它赋值 因为它必须引用一些东西 它不是真正的变量
如何真正地更改引用指向的值？结果还是要用指针

```c++
int* ref = &a;
ref = &b;
```

# 类 class / struct

**类并不会增添任何新的功能 可以用类搞定的事 不用类也一样搞得定 类只是语法糖**
面向对象编程 类只是对数据和功能组合在一起的一种方法 **有数据和处理这些数据的函数** 可以更好地维护混乱的变量和函数 对其分组

```c++
class Player
{
    int x, y;
    int speed;
};
```

这里是创建一个新的**变量类型** 这个类的名字必须是唯一的 注意结尾有`;`

```c++
Player player;
```

于是我们创建了类型为Player的变量player
player就叫作对象object或者实例instance 我们这里就是实例化了一个Player对象

`Player.x = 5;` 这会报错 成员`Player::x`不可访问
player不能访问在类Player中声明的私有成员

这是因为在创建类时 可以指定类中内容的可见性 **默认情况下都是private** 意味着只有类中的函数才能访问这些变量 但我们希望在main函数里使用这些变量 所以要改成

```c++
class Player
{
public:
    int x, y;
    int speed;
};
```

public意味着可以在类之外的任何地方访问这些变量 我们暂时不讨论可见性

现在我们希望让player移动 可以写一个单独的函数

```c++
void Move(Player& player, int xa, int ya)
{
    //xa ya是在x轴 y轴上Player移动的距离
    player.x += xa * player.speed;
    player.y += ya * player.speed;
}
```

`Player&` 要修改Player对象 所以要用引用传递

如果要调用这个函数 `Move(player, 1, -1);`

但实际上类可以包含函数 我们可以把move函数移动到类中 **类内的函数被称为方法**

```c++
class Player
{
public:
	int x, y;
	int speed;

	void Move(int xa, int ya)
    {
		x += xa * speed;
		y += ya * speed;
	}
};
```

不需要再用`Player& player`传入player对象 因为我们已经在Player对象中了 所有的x y speed 指的就是当前对象的变量

调用是 `player.Move(1, 0);`

类class和结构体struct 是只有一个关于可见度的区别 其它没有任何区别
class的成员 默认为private 除非声明public 声明`public:`之前的是private 之后的是public
struct的成员 默认为public

struct在C++中存在的唯一原因 是希望与C保持向后兼容性 因为C没有类 却有结构体

如果我想要所有成员都是public 但又不想写public这个字 应该使用结构体吗？可以 因为它们之间就只有这么一点区别 没有正确答案 只取决于编程风格

plain old data(POD) 一种**只表示变量的结构 不包含大量功能 倾向于使用struct** 这种分组只是为了让我们的代码更容易使用
比如数学上的向量类

```c++
struct Vec2
{
    float x, y;
    
    void Add(const Vec2& other)
    {
        x += other.x;
        y += other.y;
    }
};
```

无论用class还是struct 都是代表这2个浮点数的一种结构 不像之前的Player类一样 包含大量功能 **但不是说在这里不会添加方法 但添加的这个函数只用来处理这些变量 直到最后我们都只讨论这两个变量**

另外就是我们**不会倾向于在struct中使用继承**
如果要有一个完整的类层次结构 或者某种继承层次结构 倾向于使用类
继承是一种增加另一层次的复杂的东西 可**我希望我的结构体 是数据的结构**

**先在主函数中写需求 然后再回到类里写方法**

<span id="mypoint_8"></span>Log类

```c++
// 这不是一份好的代码 但是是简单的代码

#include <iostream>

class Log
{
public:
	const int LogLevelError = 0; // Error级别
	const int LogLevelWarning = 1; // Warning级别
	const int LogLevelInfo = 2; // Info级别
	// LogLevelXXX 只有XXX级别以上的日志会被打印出来

private:
	int m_LogLevel = LogLevelInfo;
	// 默认级别为Info 所有级别的日志都会被打印出来


public:
	void SetLevel(int level)
    {	// 设置日志级别
		m_LogLevel = level;
	}
	
	void Error(const char* message)
    {
		if (m_LogLevel >= LogLevelError)
			std::cout << "[ERROR]: " << message << std::endl;
	}
	void Warn(const char* message)
    {
		if (m_LogLevel >= LogLevelWarning)
			std::cout << "[WARNING]: " << message << std::endl;
	}
	void Info(const char* message)
    {
		if (m_LogLevel >= LogLevelInfo)
			std::cout << "[INFO]: " << message << std::endl;
	}
};


int main()
{
	Log log;
	log.SetLevel(log.LogLevelWarning);
	log.Warn("Hello World");
	log.Error("Hello World");
	log.Info("Hello World");
	std::cin.get();
}
//约定只打印Warning级别以上的信息 所以只输出
// [WARNING]: Hello World
// [ERROR]: Hello World
// 如果我们没有设置LogLevel 默认就是InfoLevel 全部打印出来
```

`const char*` 现在就是字符串的意思 暂时不讨论

**m_前缀 约定这是一个私有的类成员变量** 这样我们就可以区分在类中 哪些是成员变量 哪些是局部变量

可以看到 变量放在了一块 方法放在了另一块

# 静态 Static

## 类或结构体外部的static

**声明的静态函数或静态变量 只会在它被声明的cpp文件中被看到**

<span id="mypoint_7"></span>`static int s_Variable = 5;` **s_前缀 约定这是一个静态变量** **这个变量只会在这个翻译单元内部链接** 它只对这个翻译单元可见 [前面讲链接的时候](#mypoint_6) 我们就提到过static 链接器不会在这个翻译单元的作用域之外 寻找那个符号定义

```c++
// Static.cpp
static int s_Variable = 5;
```

```c++
// Main.cpp
#include <iostream>

int s_variable = 10;

int main()
{
    std::cout << s_varibale << std::endl;
    std::cin.get();
}
```

`Static.cpp`的`s_Variable`不会参与链接 这个程序不会链接报错 最后会输出10

如果Static.cpp的`static`删掉 改成

```c++
// Static.cpp
int s_Variable = 5;
```

不能正常编译 会链接报错 可以使用

```c++
// Main.cpp
extern int s_Variable;
// 之前是int s_variable = 10;
```

标志这个变量为extern 意思是它会在外部翻译单元中寻找s_Variable变量 称为external linkage或external linking 现在这样的话 s_Variable就是5 但如果Static.cpp里是`static int s_Variable = 5;` **有点像在类中声明private变量** 其他所有翻译单元都看不到这个s_Variable变量 链接器在全局作用域下 看不到这个变量

函数的static用法在[前面讲链接的时候](#mypoint_6)已经提到 使用static就可以函数名重复

什么情况下你会在class中使用private 你就什么情况下使用static静态变量 **尽量减少全局变量** 如果没有设定为static 那么链接器就会跨编译单元进行链接 **尽量将函数和变量标记为静态 除非你真的需要它们跨翻译单元链接**

## 类或结构体中的static

如果static在类或者结构体中 在类的所有实例中 **这个变量只存在一次 只有一个版本** 也就是说 你有一个类 你反复创建这个类的实例 假如你在某一个实例中修改了这个静态变量的值 那么在这个类的所有实例中 这个静态变量的值都会改变

```c++
#include <iostream>

struct Entity
{
	int x, y;
	//这里选用结构体是因为希望x y是public

	void Print() {
		std::cout << x << ", " << y << std::endl;
	}
};

int main()
{

	Entity e;
	e.x = 2;
	e.y = 3;

	Entity e1 = { 5, 8 };
	// 这是使用初始化器来实例化

	e.Print();
	e1.Print();

	std::cin.get();
}	
```

现在就只是会正常地输出2,3 5,8

结构体Entity里改成`static int x, y;` 再用`e.x` `e.y`去初始化

```c++
Entity e;
e.x = 2;
e.y = 3;

Entity e1;
e1.x = 5;
e1.y = 8;
```

报错 `error LNK2001: 无法解析的外部符号 "public: static int Entityx" (?x@Entity@@2HA) ` 是因为**静态成员变量需要在类外部进行定义和初始化**

可以在`struct Entity`后面 `int main()`前面写

```c++
int Entity::x;
int Entity::y;
```

先写作用域Entity 再写变量名x 可以不需要让它等于任何东西
现在它们就被定义了 链接器可以连接到合适的变量

我们再运行 在debug下 可以发现 我们刚刚执行完`e.x = 2;` 在e.x变成2的同时 e1.x也变成了2 哪怕我们还尚未执行到`e1.x=5;` 而在我们执行完`e1.x=5;`时 e1.x和e.x同时同步地变成了5 最后的输出结果就是 5,8 5,8

其实你可以看到 **e.x与e1.x的地址 是一样的 也就是说在所有实例中 x y都只有这么一个版本 所有实例指向的都是相同的x y 同一个地址**

所以使用e.x e1.x去使用x 是完全没有什么意义的 **可以直接使用Entity::x 恰好能表示它的唯一性** 仿佛我们是在名为Entity的namespace中创建了两个变量 实际上它们并不属于类 它们可以是private的也可以是public的 它们仍然是类的一部分 而不是namespace 但其实它们和在namespace中一样

```c++
Entity e;
Entity::x = 2;
Entity::y = 3;

Entity e1;
Entity::x = 5;
Entity::y = 8;
```

这才是它真正正确的样子 我们一直是在修改同一个变量

**类中的静态变量适用于希望在所有Entity类的实例中共享某个数据 或者将这个数据实际存储在Entity类中是有意义的 因为它与Entity有关** 为了组织良好的代码 最好是在这个类中创建一个静态变量 而不是将一些静态的或者全局的东西到处乱放

静态方法也是类似的 换成`static void print()` 那么`e.print();`就是`Entity::Print();` 但是**静态方法不能访问非静态变量** 所以如果要使用print方法 x y必须是静态变量

现在我们让x y不再是静态的 改成普通的`int x, y;` 也删掉`int Entity::x;` `int Entity::y;` 也就是e和e1分别有自己的x y 再运行就会报错 **因为静态方法没有类实例** 实际上你在类中写的每个非静态方法总是获得当前类的一个实例作为参数 通过隐藏参数发挥作用 这是类在幕后的工作方式 我们暂时不谈 所以静态方法得不到那个隐藏参数 静态方法与在类外部编写方法是相同的 就像你在类的外面写

```c++
static void Print()
{
    std::cout << x << ", " << y << std::endl;
}
```

它现在就完全不知道x y是什么 可以改成

```c++
static void Print(Entity e)
{
    std::cout << e.x << ", " << e.y << std::endl;
}
```

这个方法 是非静态类方法在编译时的真实样子

```c++
static void Print()
{
    std::cout << e.x << ", " << e.y << std::endl;
}
```

这个方法就是静态类方法使用非静态变量时的样子 所以报错 它不知道你是要访问哪个Entity的x y 每个实例的x y都是不一样的 你又没给它一个Entity的引用 即使对于静态方法调用时 你写着`e.Print();` 但实际上因为它是静态方法 等同于你写了`Entity::Print();` 所以它还是不知道要找哪个Entity的x y

## 局部static

声明一个变量 需要考虑两个问题 也就是**变量的生存期和作用域**

生存期指 在它被删除之前 它会在我们的内存中存在多久
作用域指 我们可以访问变量的范围 

<span id="mypoint_9"></span>**静态局部变量 生存期基本上相当于整个程序的生存期 但作用域只在这个函数内** 但其实它不一定非要在函数里 你可以在任何作用域里声明它 这里只是用函数举例 也可以是if语句之类的 所以函数作用域的static和类作用域的static没有太大区别 生存期基本是相同的 但是在类的作用域中 类中的任何东西都可以访问这个静态变量 但在函数作用域声明一个静态变量 它将是那个函数的局部变量 对类来说也是局部变量

```c++
void Function()
{
	static int i = 0;
}
```

意思是 当我第一次调用函数时 变量i将被初始化为0 然后所有对函数的后续调用 不会再反复创建新的变量 

```c++
#include <iostream>

void Function()
{
	static int i = 0;
	i++;
}

int main()
{

	for (int j = 0; j < 10; j++)
    {
		Function();
	}
	std::cin.get();
}
```

在debug下看这个for循环 jump in这个Function函数时 发现黄色箭头每次都跳过`static int i = 0;`这一行 直接编程将要执行`i++;` 而且即使这次循环结束了 在下一次循环执行Function函数时 i还是在那个地址没有变 而且i并不会被重置为0 毕竟黄色箭头会跳过`static int i = 0;`这一行去执行 i实际上一直在累加 变量i的生存期很长 但是一定要jump in Function函数才能看得到i的变化 监视1窗口在一遍又一遍地仅仅jump over执行for循环时 是看不到i的变化的 你必须jump in 才能看到i的更新 这也就是i的作用域仅在函数内

如果Function函数内的i并不是static i会在每次执行Function函数时 都被重置为0 i是在栈上创建的 函数作用域结束时 就会被销毁

实际上`static int i = 0;`写在函数内和写在函数外作为全局静态变量 使用起来效果是一样的 都是会一直累加 但是**写在函数内就可以增加不可见性** 变得不是大家都能使用

<span id="mypoint_19"></span>单例类 Singleton 只有一个实例的类

```c++
#include <iostream>

class Singleton
{
private:
	static Singleton* s_Instance; // 那个单例实例的指针
public:
	static Singleton& Get()
    {	// 获取那个单例实例 返回的是引用
		return *s_Instance;
	}

	void Hello() {}; // 总之是做什么事情的一个方法
};

Singleton* Singleton::s_Instance = nullptr; // 初始化单例实例的指针为nullptr

int main()
{

	Singleton::Get().Hello(); // 单例实例调用了Hello方法

	std::cin.get();
}
```

上面这个是类的静态
如果使用局部静态 main函数不变 class Singleton会变成下面这样 功能是完全一样的

```c++
class Singleton
{
public:
	static Singleton& Get()
    {
		static Singleton instance;
		return instance;
	}

	void Hello() {};
};
```

如果仅仅是`Singleton instance;` 没有static 因为Get()返回的是引用 而不是值 instance会在作用域结束之后销毁 就算返回了一个地址 那也是临时的
然而如果是static 生存期就很长了 每次我们调用Get()的时候 都会创建一个单例实例 然后返回这个已经存在的单例实例 这个单例实例将长时间存在 但是对于多个实例的类就没办法写这样的Get()创建 因为static就只能创建并维护这一个实例

不一定是非要Singleton 比如写一个静态初始化函数来创建所有对象 那就可以使用静态Get()方法 

> 感觉static的这几种用法都是为了 本可以全局的东西 却自己在内部暗中使用 而其他人甚至不可见 你这个人真是只想着自己呢
>
> 1. 在文件内自己偷偷用 其他文件不知道它的存在 不参与链接
>    我起了个和外面的人一模一样的名字 大家却完全不知道
> 2. 类的所有实例之间通用共享 被类存储管理着 大概算是属于这个类吧 其实大家都可以用啦 只是用的时候要记得去写这个类的名字
> 3. 作用域内持续长时间地使用 作用域之外不可见
>    明明一直占着存储空间却不被大家发现喵>\_<

# 枚举 enum

其实就是数值的集合 是给一个值命名的一种方法 将一组数值集合作为类型 而不仅仅是用整型作为类型

```c++
#include <iostream>

enum Example
{
	A, B, C
};

int main()
{

	Example value = B; // 赋值必须是A B C中的一个

	if (value == 1)
    {	// 现在value等于B 就是1
		// Do something
	}

	std::cin.get();
}
```

此时默认的A是0 B是1 一个接一个地递增
也可以初始化它 比如`A = 0, B = 2, C = 6` 
如果是从一个非0数开始 `A = 5, B, C` 那么默认就是B=6 C=7

枚举默认是32位int整型 但也可以指定类型 但必须是整型 不能是浮点数

```c++
enum Example : unsigned char
{	// 8位整型
	A = 5, B, C
};
```

枚举是给特定的值命名的一种方式 这样就不必在各种地方 处理各种整数

[Log类](#mypoint_8)的3个级别 只是整数1 2 3 可以修改成枚举

```c++
public:
    enum Level
    {
        LevelError = 0, LevelWarning, LevelInfo
    };
private:
	Level m_LogLevel = LevelInfo;

// 原本是
// public:
// 	const int LogLevelError = 0; // Error级别
// 	const int LogLevelWarning = 1; // Warning级别
// 	const int LogLevelInfo = 2; // Info级别
//
//private:
//	int m_LogLevel = LogLevelInfo;
```

倾向于显式地写成=0 虽然它默认就是=0 仅仅为了提高代码可读性
使用Level就可以把m_LogLevel限制在枚举的那几个数字中 本例中就只能是0 1 2 后面涉及到level的也都要改成Level类而不是int

在主函数里调用时 不再用`log.LogLevelError` 而是`Log::LevelError` 因为我们在Log这个类的命名空间中 有一个枚举数叫Error 枚举Level本身并不是一个命名空间 不是枚举类 暂时先不讲枚举类 所以Error Warning Info只存在于这个Log类中

枚举其实就是整数

# 构造函数

```c++
class Entity
{
public:
    float X, Y;
    
    void Print()
    {
        std::cout << X << ", " << Y << std::endl;
    }
};

int main()
{
    Entity e;
    e.Print();
    std::cin.get();
}
```

输出的是`-1.07374e+08, -1.07374e+08` 由于未初始化 X的值是未定义的随机值 在 Print 方法中访问了未初始化的X和Y 我们得到的是那个内存空间中原来的那些东西 暂时我们不讲类初始化

X是public的 如果在主函数里直接用`std::cout << X << std::endl;`输出 就会报错 未初始化局部变量

因此需要初始化

```c++
class Entity
{
public:
    float X, Y;
    
    void Init()
    {
        x = 0.0f;
        Y = 0.0f;
    }
    
    
    void Print()
    {
        std::cout << X << ", " << Y << std::endl;
    }
};

int main(){
    Entity e;
    e.Init(); // 在这里初始化
    e.Print();
    std::cin.get();
}
```

但这样很麻烦 每次实例化之后都要再接一句初始化 有点麻烦了 就需要构造函数

构造函数是每次构造一个对象时都会调用的方法 **实例化时被调用 如果不实例化 就不会运行** 没有返回类型 名称必须与类的名称相同 可以有参数 也可以是完全空白

```c++
class Entity
{
public:
    float X, Y;
    
    Entity()
    {
        X = 0.0f;
        Y = 0.0f;
    } // 不再需要init方法了
    
    void Print()
    {
        std::cout << X << ", " << Y << std::endl;
    }
};
```

现在再`Entity e;` 它默认就是有初始化的

如果不指定构造函数 它也有构造函数 也就是默认构造函数 也就是

```c++
Entity(){
}
```

什么都不会做 C++并不会把int float自动初始化为0 必须手动初始化 

在类里可以写很多构造函数 当然参数需要是不一样的 这叫**函数重载 即有相同的函数/方法名 但有不同参数的不同函数版本**

```c++
Entity(float x, float y)
{
    X = x;
    Y = y;
}
```

现在可以用参数实例化并初始化了 `Entity e(10.0f, 5.0f)` 

如果使用new关键字来实例化（堆内存） 它也会调用构造函数 

如果只希望别人用静态的方法 不能实例化

```c++
class Log{
private:
    Log() = delete; // 构造函数被删除了
public:
	static void Write()
    {
        
    }
}
```

我只想让别人这样用我的Log类 `Log::Write();` 不希望别人实例化

# 析构函数

和构造函数很相似 是在销毁对象时被调用
构造函数是设置变量 或者做任何所需的初始化
析构函数是卸载变量等东西 并清理使用过的内存 

析构函数也适用于栈和堆分配的对象
如果用new分配一个对象 调用delete 析构函数会被调用
如果是栈对象 作用域结束时 栈对象将被删除 这时 析构函数也会被调用

```c++
class Entity
{
public:
    float X, Y;
    
    Entity()
    {
        X = 0.0f;
        Y = 0.0f;
        std::cout << "Created Entity!" << std::endl;
    }
    
    ~Entity()
    {
        std::cout << "Destoryed Entity!" << std::endl;
    }
    
    void Print()
    {
        std::cout << X << ", " << Y << std::endl;
    }
};

int main(){
    Entity e; // 这是栈分配
    e.Print();
    std::cin.get();
}
```

析构函数前面有`~`

这个例子中`float X, Y;` 我们在为这两个浮点变量申请内存时 完全没有考虑之后怎么清除内存 暂时不讨论内存分配

只有主函数退出时 析构函数才会被调用 所以也看不到析构函数打印的那句话 都放到函数里

```c++
class Entity
{
    // 和上面的一样 不再复制
}

void Function()
{
    Entity e;
    e.Print();
}

int main(){
    Function();
    std::cin.get();
}
```

因为`Entity e;`是在栈上创建的 所以在Function作用域结束之后就销毁 即在`std::cin.get();`未执行时 就已经输出了`Destoryed Entity!`

在函数也可以放断点 调用到这里的时候就会暂停

为什么要使用析构函数？
如果已经在堆上手动分配了任何类型的内存 那么需要手动清理
如果在Entity类使用中或者构造中分配了内存 需要析构函数来删除内存 因为当析构函数调用时 Entity实例对象就消失了

也可以手动调用析构函数 但是很少这样做 `e.~Entity();`
对于本例 调用析构函数其实也就只是打印 并没有释放什么资源 内存的释放其实是随着栈内存的作用域结束 自动释放的

# 继承

相互关联的类的层级结构 有一个包含公共功能的基类 防止代码重复 然后从基类或者父类派生一些类

比如游戏中 每一个实体都有自己的位置

```c++
class Entity
{
public:
    float X, Y;
    
    void Move(float xa, float ya)
    {
        X += xa;
        Y += ya;
    }
};

class Player : public Entity
{
public:
    const char* Name;
    
    void PrintName()
    {
        std::cout << Name << std::endl;
    }
};
```

任何Entity类中不是私有的东西 都可以被Player类访问 在Player类里只需要写新的东西

暂时我们不讨论多态 多态的意思是 一个单一类型 但有多个类型 Player不仅是一个Player 也是一个Entity 所以我们可以在任何想要使用Entity的地方使用Player 可以把Player类的实例传给适用于Entity类作为参数的函数
也可以改变父类或者基类的行为 比如重写一个方法 用新的代码来代替父类方法运行

# 虚函数

虚函数允许我们在子类中重写方法
B是A的子类 如果在A类中创建一个方法 标记为vitual 就可以在B类中重写这个方法

```c++
class Entity
{
public:
    std::string GetName() { return "Entity"; }
};

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}
    
    std::string GetName() { return m_Name; }
}

int main()
{
    Entity* e = new Entity();
    std::cout << e->GetName() << std::endl;
    
    Player* p = new Player("123");
    std::cout << p->GetName() << std::endl;
    
    Entity* entity = p;
    std::cout << entity->GetName() << std::endl;
    
    std::cin.get();
}
```

1. `Player(const std::string& name) : m_Name(name) {}`
   构造函数接受一个常量引用参数name
   `:` 表示初始化列表开始
   `m_Name(name)` 表示用参数name初始化成员变量m_Name
   成员变量m_Name在对象创建时直接通过参数构造 而非先默认构造再赋值 避免默认构造 + 赋值的双重操作
   等效于 先默认构造 再赋值

    ```c++
    Player(const std::string& name)
    {
        m_Name = name;
    }
    ```
   
2. `Entity* e = new Entity();`
   new Entity()会在堆上动态分配一个Entity对象 并返回其内存地址/指针 因此必须用指针变量Entity*来接收
   堆上动态分配 `Entity* e = new Entity();`搭配`e->GetName();`
   或者在栈上创建 `Entity e;` 搭配`e.GetName();`
3. `->`是指针访问成员的语法糖 `e->GetName()`等效于`(*e).GetName()`
4. `Entity* entity = p;`
   p是Player类型的指针 把它赋值给了Entity类型的指针entity 是基类指针直接指向派生类对象 这是安全的 称为向上转型 Player对象的内存布局中包含Entity的基类部分

目前这段代码会输出

```c++
Entity
123
Entity //并不是123
```

`Entity* entity = p;` 为什么`entity->GetName()`会得到entity而不是123？
我们可以知道 entity和p都是指针 通过赋值 它们的地址一定是相同的 但是p能访问m_Name 而entity不能 entity的静态类型是Entity* 编译器只允许通过它访问Entity类的成员 比如GetName()无法直接访问Player类的m_Name

但我们希望C++能知道这个Entity实际上是Player 让它调用Player的GetName 因此需要虚函数 Dynamic Dispatch 动态联编 通过v表/虚函数表来实现编译 v表就是一个表 包含基类中所有虚函数的映射 这样就可以在运行时 将它们映射到正确的覆写/override函数 如果想覆写一个函数 就必须**将基类中的基函数标记为虚函数 在前面加上virtual 将覆写函数标记为关键字override** 只有虚函数才能被overrdie

```c++
class Entity
{
public:
    virtual std::string GetName() { return "Entity"; } // 修改了
};

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}
    
    std::string GetName() override { return m_Name; } // 修改了
}
```

虚函数是有运行成本的 首先需要额外的内存来存储v表 这样就可以分配到正确的函数 基类中要有一个成员指针 指向v表 以及每次调用虚函数时 要遍历这个表 来确定要映射到哪个函数

虚函数（virtual）是C++实现运行时多态的关键机制 它的核心原理是

- 虚表（vtable）：每个包含虚函数的类都有一个虚表 本质是一个函数指针数组 存储该类所有虚函数的实际地址
- 虚表指针（vptr）：每个对象内部隐含一个指针（vptr） 指向其所属类的虚表 

在运行时 通过对象的vptr找到虚表 再通过虚表索引调用正确的函数实现

内存布局：

- Entity对象：
  ```plaintext
  | vptr (指向 Entity 的虚表) | Entity 其他成员... |
  ```

- Player对象：
  ```plaintext
  | vptr (指向 Player 的虚表) | Entity 基类成员... | Player 成员（如 m_Name）... |
  ```

虚表内容：

- Entity的虚表：
  ```plaintext
  [0] Entity::GetName 的地址
  ```

- Player的虚表：
  ```plaintext
  [0] Player::GetName 的地址  // 覆盖了基类的函数地址
  ```

当执行`entity->GetName()`时：  
1. 获取vptr：通过entity指针找到对象的vptr（位于对象内存起始位置）
2. 查找虚表：通过vptr找到所属类的虚表 而entity也就是p的这个地址的起始位置 存储的其实仍然是Player的虚表 所以会调用到Player的GetName
3. 调用函数：从虚表中按索引（例如索引0对应GetName）取出函数地址 调用 `Player::GetName()`

在debug下 指针p和指针entity 的值是同一个地址 而且现在entity和p的值除了地址也都会显示m_Name=123 entity显示的类型是Entity\*{Player} 在使用虚函数之前 entity是看不到m_Name的 类型也只是Entity\*

内存窗口显示这个地址的内容是 64位小端序 vtpr要看前8字节 vtpr就是 `18 ec 77 35 f7 7f 00 00` 那就是地址0x7FF73577EC18

到这个地址去看 这就是Player类的虚表 前8个字节是`95 16 77 35 F7 7F 00 00` 那么函数Player::GetName地址就是0x7FF735771695 在内存窗口输入&Player::GetName又不是这个地址 最后两个字节不一样 是因为编译器在虚表中插入了调整this指针的代码片段 称为 Thunk 而非直接存储函数地址 这是MSVC实现多态时的常见行为 尤其在涉及虚函数覆盖或特定内存布局时

# 接口/纯虚函数 interface

纯虚函数允许我们在基类中定义一个没有实现的函数 然后强制子类去实现该函数

接口类只包含未实现的方法 所以基本上不能实例化

```c++
class Entity
{
public:
    virtual std::string GetName() = 0; //修改了
};

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}
    
    std::string GetName() override { return m_Name; }
}
```

仍然是virtual `=0` 意味着它必须在一个子类中实现

它还是一个类 是class 不是interface 是一个只有虚函数的类 C++没有Interface关键字 接口只是C++的类

现在这样不能实例化Entity 现在Player里实现了GetName 所以还可以实例化 如果没有实现 Player也不能实例化

```c++
class Printable
{
public:
	virtual std::string GetClassName() = 0;
};

class Entity : public Printable
{
// 要让Entity实现GetClassName()
public:
    virtual std::string GetName() { return "Entity"; }
    std::string GetClassName() override { return "Entity"; }
}

class Player : public Entity
{
private:
    std::string m_Name;
public:
    Player(const std::string& name)
        : m_Name(name) {}
    
    std::string GetName() override { return m_Name; }
    
    std::string GetClassName() override { return "Player"; }
}

void Print(Printable* obj)
{
    std::cout << obj->GetClassName() << std::endl;
}
```

只要某个Printable的子类没有覆写GetClassName() 这个类就无法实例化
Player已经是Entity的子类了 Entity里已经实现GetClassName() 这里不用再实现 如果不是子类的话 就要写成`class Player : public Entity, Printable`
Printable子类的每一个实例都同时也是一个Printable 所以都可以作为Print()的参数传进去

# 可见性

谁能看见它们 调用它们 可见性是对程序实际运行方式和程序性能都完全没有影响 可见性并不是你的CPU需要知道的东西 计算机是不知道的 只是为了方便组织代码

private protected public

private就是只有自己这个类内部可见 这个类的实例不可见 继承了这个类的子类也不可见 但是还有这个类的friend这种东西 也可以对private内容读取和写入 暂时不讨论

protected比private更可见 比public更不可见 这个类和它的子类可见 这个类的实例不可见

public 所有人都可以访问

可见性只是给人用的 在使用一个类的时候 只被允许使用public的东西 确保人们不会调用他们不应该调用的代码 因为有可能破坏其它东西 也可以给自己用 可以看到自己代码的设计意图 想要的访问和使用类的方式

# 数组

```c++
int example[5];
example[0] = 2;

std::cout << example[0] << std::endl;
std::cout << example << std::endl;
```

example其实是一个指针 会返回这个数组的首地址
example[0]是int

如果访问example[] 0-4以外的值 debug下会提示内存访问违规 release下不会报错 只是写入了不属于你的内存 所以要在数组边界内读写

数组常常与for循环结合

```c++
for(int i=0; i<5; i++) // 不要写成i<=4 性能开销更大 不仅要做小于的比较 还要做等于
    example[i] = i;
```

debug下在内存窗口访问&example 可以看到00 00 00 00 01 00 00 00 02 00 00 00 03 00 00 00 04 00 00 00 cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc

注意到数组是连续的内存 小端序现在已经填充上了01234 每个数据都是int 4字节

通过example[i]来访问特定索引时 实际上是对example这个指针的地址取了一个偏移量bias 比如对于example[2] 就是对这个地址+2*4字节(int)的偏移量

数组实际上就是一个指针 本例中是整型指针
所以你也可以完全这样做

```c++
int* ptr = example;

example[2] = 5;
*(ptr + 2) = 6;
```

ptr+2不是加2个字节的 而是加了2*4个字节 因此`*(ptr + 2)`就是`example[2]` 先后把它修改成了5和6

指针的加法操作不是按字节数加法 而是针对这个指针的数据类型进行 ptr是int类型的指针 于是ptr+2 的结果是指针移动2个int的距离 不是加2个字节 是移动 2\*sizeof(int) 个字节

如果真的想**对字节进行操作 就把指针转换成一个字节的char类型** 做偏移 最后要把它转回int类型的指针 才能对它赋值

```c++
*(int*)((char*)ptr + 2*sizeof(int)) = 6;
```

也可以在堆上创建数组 

```c++
int example[5]; // 栈创建

int* another = new int[5]; // 堆创建
delete[] another;
```

这两种创建的含义是一样的 但是生存期不同 栈创建离开作用域就会被销毁 堆创建在我们手动销毁时才会消失 必须用delete删除 因为是用数组操作符`[]`分配的 所以也要用它删除

<span id="mypoint_11"></span>最大的差异就是生存期 比如某个函数返回的是在这个函数中创建的数组 其实就是**返回了指针 就必须用堆创建 返回的地址才有效** 也可以联想到使用[局部static](#mypoint_9) 静态变量不会在函数返回后被销毁 避免了**悬空指针** 但是比如

```cpp
int* badExample()
{
    int x = 10;
    return &x; // 
}
```

返回栈变量的地址 离开函数后x被销毁 指针失效 如果改成`static int x = 10;`

- 共享状态：

  静态变量在多次调用中共享同一内存

  ```c++
  int* p1 = badExample(); // p1 指向的 x = 10
  *p1 = 20;              	// 修改 x 的值为 20
  int* p2 = badExample(); // p2 也指向 x，此时 x = 20
  ```

  所有调用者共享同一个x 可能导致意外的数据污染

- 线程安全问题：

  如果多线程同时调用 badExample()并修改x 需要加锁保护 否则可能导致数据竞争

堆内存还有**间接寻址**

```c++
class Entity
{
public:
	int example[5];
	
    Entity()
    {
        for(int i=0; i<5; i++)
            example[i] = i;
    }
};

int main()
{
    Entity e;
    
    std::cin.get();
}
```

如果是栈创建 在内存窗口查看e的地址 就是00 00 00 00 01 00 00 00 02 00 00 00 03 00 00 00 04 00 00 00 cc cc cc

改成堆创建

```c++
class Entity
{
public:
	int* example = new int[5];
	
    Entity()
    {
        for(int i=0; i<5; i++)
            example[i] = i;
    }
};
```

再去内存窗口查看e的地址 就是70 62 38 94 c7 02 00 00 cc cc cc cc 这是小端序 也就是要再进入地址0x000002c794386270 在这个地址我们才看到00 00 00 00 01 00 00 00 02 00 00 00 03 00 00 00 04 00 00 00 fd fd fd 这是间接寻址 这样在内存中跳跃肯定会影响性能 尽量使用栈创建

C++11内置数据结构 std::array 而我们现在用的是原始数组 不能计算原始数组的大小

```c++
int a[5];
```

这是栈创建 可以这样计算
`int count_a = sizeof(a) / sizeof(int);`
sizeof(a)返回的是数组占多少字节 本例是20 除掉sizeof(int) 才是数组中元素的计数 count_a最后就是20/4=5

**一般使用count表示元素个数 size表示字节数**

但如果是堆创建

```c++
int* b = new int[5];
```

`sizeof(b)`得到的是一个int型指针的大小 没有办法像栈分配那样计算

但是倾向于不要这样去算 还是自己维护数组大小
```c++
const int exampleSize = 5;
int example[exampleSize];
```

但是你这么写就会报错 这是**C++的问题 在栈中为数组申请内存的时候 数组的大小必须是一个编译时就要知道的常量** 所以还记得在C语言中 一般我们把这个const int设置成全局变量 要么就写成define宏定义 所以在这里要写成

```c++
static const int exampleSize = 5;
int example[exampleSize];
```

```c++
// 需要 #include <array>
std::array<int, 5> another;
```

这是C++11的数组 int是类型 5是数组大小
调用数组大小就用`another.size()` 当然因为它有这种很多功能 开销会比原始数组会更大 但通常是值得的 使用std数组会比使用原始数组更安全

暂时我们先不讨论这种数组

# 字符串

一个字符是一个字节 ASCII码 其他语言字符也许不止一个字节 有其它编码 如果用1个字节8bit编码 能表示256个字符 对于中文远远不够 2个字节16bit编码就是65536个 暂时我们不讨论字符编码 字体渲染

通常字符串里就是很多1个字节的字符 字符串其实就是char类型的字符数组

```c++
char* name = "123";
name[2] = 'a';
```

但是编译器是不推荐我这么做 告诉我要改成`const char*` 因为**字符串字面量是存储在内存的只读部分的 试图修改会导致未定义行为**

无论如何"123"其实是一个const char[4] 隐藏的最后一个字节是0 称为空终止字符 是字符串结束的地方 其实我们不知道字符串到底有多少个字符 就靠从指针开始直到终止符0来计算

"123" 其实是const char[4] 因为字符串最后有一个空终止符'\0' 不是字符0 而是就是0 NULL "123"就是字符串字面量 

```c++
const char name[4] = "123";
const char* name = "123";
// 这两种写法都可以
```

```c++
const char* name = "123";
const char name2[3] = {'1', '2', '3'};
```

字符是单引号 双引号是`char*` 不是字符串 是指针

第一行在内存窗口查看是 31 32 33 00 00 00
第二行在内存窗口查看是 31 32 33 cc cc cc
输出name2得到的是123烫烫烫烫烫烫烫烫 其实就是一堆随机字符 因为没有空终止符 cout就不知道打印到哪里结束 如果写成 `const char name2[4] = {'1', '2', '3', '\0'};` 写'0'或者'\0'都可以 在ASCII码里 0对应的是NULL 或者直接写数字0 因为ASCII码里字符'0'对应的就是0 现在就能正确地打印123

C++标准库有std::string 它只是一个char* 是一个char数组和一些用来操作char数组的函数 

```c++
// #include <string>
std::string name = "123"
```

其实就是把`const char*`换成了`std::string` string有一个构造函数 接收`char*`或者`const char*`参数 现在name其实是一个const char数组 不是char数组 定义字符串时 双引号里的很多字符 在C++里就是const char数组

string也有很多方法 比如可以调用`name.size()`

- 字符串附加 append

```c++
// 错误代码
std::string name = "123" + "hello";
```

但是双引号里的时const char数组 不是字符串 两个指针不能相加

```c++
std::string name = "123";
name += "hello";
```

现在就是将一个指针加到了name上 `+=`这个操作符在string类中被重载了 所以可以这样写 也可以写成

```c++
std::string name = std::string("123") + "hello";
```
必须将一个操作数显式地转换为std::string 因为C++不允许两个`const char*`直接相加

```c++
using namespace std::string_literals;
std::string name = "hello"s + " world";
std::string name = u8"hello"s + u8" world";
std::wstring name = L"hello"s + L" world";
std::u32string name = U"hello"s + U" world";
```

"hello"s中的s是一个用户定义的字面量 将字符串字面量（如"hello")转换为std::string对象 这个功能来自于C++14中的`std::string_literals`命名空间

`"hello"s`是用户定义字面量 等效于`std::string("xxx")`

1. 第一个操作数：`"hello"s`通过用户定义字面量转换为`std::string`对象

2. 第二个操作数：`" world"`是`const char*`类型

3. 运算符重载：`std::string`类定义了以下重载：

   ```
   std::string operator+(const std::string& lhs, const char* rhs);
   ```

4. 隐式转换：右侧的`const char*`会自动转换为`std::string`临时对象

原始内存布局：
"hello" -> ASCII码：68 65 6C 6C 6F 00
" world" -> 20 77 6F 72 6C 64 00

操作过程：

1. 创建"hello"s的std::string（分配堆内存）
2. 创建临时std::string(" world")
3. 执行operator+，分配新内存合并内容
   最终结果：hello world

**仍然建议把每一个都显式地写出后缀s**

```c++
const char* name = u8"123"; // 普通的const char 1个字节8bit的字符 utf8
const wchar_t* name2 = L"123"; // 宽字符 反正不是1字节 可能是2字节 可能是4字节 取决于编译器

const char16_t* name3 = u"123"; // 2个字节16bit的字符 utf16
const char32_t* name4 = U"123"; // 4个字节32bit的字符 utf32
```

utf-8 变长编码（1-4字节） 1个ASCII字符占1字节 1个汉字通常占3字节

```c++
// 方法1
const char* example = R"(Line1
Line2
Line3)";

// 或者写
std::string example = R"(Line1
Line2
Line3)"s;  // 注意这里需要后缀s
```

**处理多行文本最优先使用**`R"(...)"` 直接保留所有换行符 不用写\n \t

```c++
// 方法2
const char* example = "Line1\n"
    "Line2\n"
    "Line3\n";

// 或者写
using namespace std::string_literals;
std::string example = "Line1\n"s
    "Line2\n"s
    "Line3\n"s;
// 注意这里需要后缀s 当然也可以只写一个后缀s

// 如有可能 也可以不拼接
std::string example = "Line1\nLine2\nLine3\n"s;
```

相邻字符串字面量自动拼接 等效于"Line1\nLine2\nLine3\n" 这种写法要手动写\n

```c++
// 方法3 和方法2相比就是多了+ 这是我们最开始最原始的方法
std::string example = std::string("Line1\n") + 
                 "Line2\n" + 
                 "Line3\n" + 
                 "Line4";

// 或者写
std::string example = "Line1\n"s + 
                 "Line2\n" + 
                 "Line3\n" + 
                 "Line4";
```

- 查询name字符串里是否包含'lo'

```c++
bool contains = name.find("lo") != std::string::npos;
```

`std::string::npos;`表示一个不存在的位置 `name.find("lo")`返回的是lo所在的首位置

- 把字符串传给其它函数 

```c++
void PrintString(std::string string)
{
    string += "h";
    std::cout << string << std::endl;
}
```

传的不是引用 只不过是把传入的string复制到了函数里 不会影响到传递的原始string 但是字符串的复制是很浪费时间的 所以即使实现的功能是通过只读就能完成 也尽量通过常量引用传递

```                                                                                                   c++
void PrintString(const std::string& string)
{
    string += "h";
    std::cout << string << std::endl;
}
```

<span id="mypoint_10"></span>**const T& 常量引用是引用 所以不用复制 const表示我们不会修改它 是只读访问** 在大型对象适用 而对于内置类型如int double 复制成本低 直接传值会更高效 暂时不过多讨论

# CONST

有点像类和结构体的可见性 是一个承诺 承诺一些东西是不变的 是常量不是变量

```c++
const int MAX_AGE = 90;

const int* a = new int;

a = &MAX_AGE; // 合法
*a = 2; // 不合法

// int* const a = new int;
//
// a = &MAX_AGE; // 不合法
// *a = 2; // 合法
```

1. `const int* a`或者`int const* a`
   const在\*左边 表示指针指向的内容是常量 而指针本身可变
   表示**a是一个指向常量int的指针 指针a本身不是常量 因此可以重新指向其他地址 但\*a是常量 无法对\*a进行修改**

2. `int* const a`
   const在\*右边 表示指针本身是常量 不能改变指向的地址 但指向的内容可以修改
   表示**int型指针a是一个常量 指针指向的地址是不能改变的 但是可以修改指针指向的内容 a是常量 \*a不是常量**

3. `const int* const a`
   两个const分别修饰指针和内容 两者都不可变
   表示a是一个指向常量int的常量指针 不能修改指针指向的内容 也不能修改指针指向的地址

```c++
class Entity
{
private:
    int m_X, m_Y;
public:
    int GetX() const
    {
        m_X= 2; // 不合法
        return m_X;
    }
};
```

在类的方法名之后const 意思是这个方法不会修改任何实际的类

```c++
class Entity
{
private:
    int* m_X, m_Y; // m_X是指针 m_Y是int 不是指针
    int* m_X, *m_Y; // m_X m_Y都是指针
public:
    const int* const GetX() const
    {
        // GetX()返回的东西是 指向常量int的常量指针
        // 同时GetX()方法不会对类进行修改
        return m_X;
    }
};
```

后缀const的方法是只读 使用的时候可以传[常量引用](#mypoint_10) 就不用复制

```c++
void PrintEntity(const Entity* e)
{
    // e现在是一个指向常量Entity的指针
    // 可以修改指针指向的地址 但不能修改它指向的内容 也就是*e
    e = nullptr; // 合法
    std::cout << e.GetX() << std::endl;
}

// 如果通过常量引用传参 也是一样
void PrintEntity(const Entity& e)
{
    // e是一个引用
    // 写e=XXX 并不能修改它指向的内容
    // 因为引用只能在创建的时候初始化指定
    // 并不能后续修改它指向的内容
    // e=XXX 就只是在修改e指向的那个东西
    // 也就实际上等同于是在修改指针指向的内容
    // 既然声明了它指向的东西是const 就不能修改
    e = Entity(); // 不合法 不能修改它的内容
    std::cout << e.GetX() << std::endl;
}
```

```c++
class Entity
{
private:
    int m_X, m_Y;
public:
    int GetX() const
    {
        return m_X;
    }
};

void PrintEntity(const Entity& e)
{
    std::cout << e.GetX() << std::endl;
}
```

如果GetX()不后缀const 在PrintEntity里就不能调用GetX() 因为GetX已经不能保证它不会修改Entity（该方法中也就是e） 我没有直接修改e 但我调用一个可以修改e的方法 这也不允许 所以要把方法标记为const

所以可以写两个版本的GetX()

```c++
class Entity
{
private:
    int m_X, m_Y;
public:
    int GetX() const
    {
        return m_X;
    }
    
    int GetX()
    {
    	return m_X;
    }
};
```

PrintEntity就会默认使用GetX的const版本

所以**如果实际上你的方法没有修改类 或者它们不应该修改类 要总是标记这个方法为const** 这样常量引用才能使用你的方法

```c++
class Entity
{
private:
    int m_X, m_Y;
    mutable int var;
public:
    int GetX() const
    {
        var = 2;
        return m_X;
    }
};
```

我们现在在const方法里修改了类成员变量 因为var是mutable

**mutable允许函数是常量方法 但可以修改变量** 基本上在类成员中这样使用 就是它唯一的用法了

也可以用在[lambda](#mypoint_16)中

```c++
int main()
{
    int x = 8;
	auto f = []()
    {
    	std::cout << "Hello" << std::endl;
	}
    
    f();
}
```

lambda基本上就像一个一次性的小函数 可以写出来并赋值给一个变量 可以像调用函数一样使用它

<span id="mypoint_15"></span>lambda表达式 匿名函数 []是lambda的捕获列表 用于控制lambda如何访问外部作用域的变量

```c++
auto f = [捕获列表](参数列表) { 函数体 };
```

1. [x] 值捕获
   将外部变量x的值复制到lambda中 在lambda内部修改的是副本 不影响外部变量
2. [&x] 引用捕获
   通过引用捕获外部变量x 在lambda内部修改的是原始变量
3. [=] 默认值捕获
   捕获所有外部变量的副本 适用于需要读取外部变量但不想修改它们的场景
4. [&] 默认引用捕获
   捕获所有外部变量的引用 适用于需要修改外部变量的场景
5. [] 什么都不捕获

```c++
int main()
{
    int x = 8;
	auto f = [=]()
    {
        // x++; 直接这样修改是错的 因为在lambda里的是副本 默认是const 修改不了
        int y = x;
        y++;
    	std::cout << y << std::endl;
	}
    f();
}
```

但是这样写也比较麻烦 **改用mutable 就可以修改了 但在lambda之外 x仍然是原来的值 因为不是引用传递的**

```c++
auto f = [=]() mutable
{
    x++;
    std::cout << x << std::endl;
}
```

其实这也很不常用 基本上mutable就是在const里用的

# 构造函数初始化列表

这是在构造函数中初始化类成员变量的一种方式

```c++
class Entity
{
private:
    std::string m_Name;
public:
    Entity()
    {
        m_Name = "Unknown";
    }
    Entity(const std::string& name)
    {
        m_Name = name;
    }
};
```

这是我们平时用的初始化方法

但是C++还有另一种方法

```c++
class Entity
{
private:
    std::string m_Name;
    int m_Score;
public:
    Entity()
        : m_Name("Unknown"), m_Score(0)
    {
        
    }
    Entity(const std::string& name)
        : m_Name(name), m_Score(0);
    {
        
    }
    // 也都可以不缩进 写成比如
    // Entity() : m_Name("Unknown"), m_Score(0); {}
};
```

**初始化列表要按类成员变量声明的顺序写**

**应该永远到处使用初始化列表去初始化**

# 三元操作符

只是if语句的语法糖

```c++
static int s_Level = 1;
static int s_Speed = 2;

int main()
{
    if (s_Level > 5)
        s_Speed = 10;
    else
        s_Speed = 5;
    
    // 更易读的做法 为了避免考虑优先级 用括号吧
    s_Speed = (s_Level > 5 && s_Level < 100) ? 10 : 5;
    
    std::string rank = s_Level > 10 ? "Master" : "Beginner";
    
    std::cin.get();
}
```

# 创建并初始化C++对象

```c++
using String = std::string;
// 这样就不用到处写std::string 直接写String 因为不想用std命名空间

class Entity
{
private:
    String m_Name;
public:
    Entity() : m_Name("Unknown") {}
    Entity(const String& name) : m_Name(name) {}
    
    const String& GetName() const
    {
        return m_Name;
    }
};

int main()
{
    Entity e1; // 在栈上创建
    // 这时e1已经用默认构造函数初始化了 并不是没有初始化
    Entity e2("123");
    
    std::cin.get();
}
```

`Entity e2 = Entity("123");` 拷贝初始化
使用=进行初始化 语法上会先构造一个临时对象 再通过拷贝/移动构造函数初始化目标对象 C++17开始 编译器会强制省略临时对象的拷贝 称为拷贝省略 直接构造目标对象

`Entity e2("123");` 直接初始化 **优先使用**
使用括号参数列表直接调用构造函数 没有中间临时对象的拷贝步骤

栈创建 在作用域结束就销毁 但是作用域不止是函数 有{}就算

而且如果会创建很多对象 栈太小了 不够存储

```c++
int main()
{
    Entity* e = new Entity("123"); // 堆创建
    std::cout << (*e).GetName() << std::endl;
    delete e;
    std::cin.get();
}
```

`Entity* e = new Entity("123");`
`new Entity`会返回一个指针 是这个Entity在堆上被分配的内存地址 所以要用`Entity *`
但是这是Java/C#风格 虽然C++也可以这样写 但是你也要负责释放这些内存 `delete e;`
C# 即使用的是new关键字 所有的类都是在栈上分配
Java 所有东西都在堆上
**不能到处使用new**

因为e现在是指针 在调用函数时 就要用`(*e).GetName()` 或者`e->GetName()` 这个`->`箭头运算符暂时不讨论

如果要创建的对象很大 或者希望显式地控制对象生存期 就用堆创建 否则用栈创建 **尽量用栈** 或者用智能指针 暂时不讨论

# new

写C++就应该关心内存 性能 优化问题

new的主要目的是在堆上分配内存

写一个new int 需要4个字节的内存 就需要寻找4个字节内存的连续块 但并不是一行一行搜索内存看有没有4字节连续内存 而是有**空闲列表** 会维护那些有空闲字节的地址 暂时不过多讨论 如果找到了 它就返回一个指向这个内存的指针 这样就可以开始使用了

```c++
int a = 2;
int* b = new int;
int* c = new int[10]; // 10个元素的数组 40字节

Entity* e1 = new Entity(); // 已经默认构造函数初始化
Entity* e2 = new Entity[10]; // Entity型的数组

delete e1;
delete[] e2;
```

- `Entity* e = new Entity();` 值初始化 **优先使用**
  类的成员变量中
  内置类型比如int float 指针等 会**零初始化**
  类类型比如std::string 会调用默认构造函数

- `Entity* e = new Entity;` 默认初始化
  类的成员变量中 内置类型不初始化 随机垃圾值
  类类型比如std::string 会调用默认构造函数

`Entity* e = new Entity[10];` 看看Entity类有多大 因为是数组 再×10 需要这么多内存 连续分配10个Entity 然后调用初始函数

new其实是一个操作符 就像 + - = 所以可以**重载**这个操作符 其实只是类似一个函数 分配一定大小的内存 然后返回空指针 `void*` 一个没有类型的指针 指针只是一个内存地址 指针之所以需要类型 是因为你需要类型才操纵它 知道需要从这个地址开始读取多长的内存 但其实指针只是一个内存地址 一个数字 所以可以根本不需要什么类型 

通常 调用new会调用隐藏在里面的C函数malloc 相当于我们写了 `Entity* e = (Entity*)malloc(sizeof(Entity))` 用malloc分配了一个sizeof(Entity)大小的内存 返回void指针 再转换为Entity类型 但是和`Entity* e = new Entity[10];`的区别就是 使用new会调用Entity构造函数 而malloc只是分配内存 **还是优先使用new**

使用new 要记得使用delete 其实这也是一个操作符 调用的C函数free 释放malloc申请的内存

new之后 内存没有被释放 不会被放回空闲列表 不能再被new调用后再分配 直到我们调用delete 必须手动操作

placement new
没有真正分配内存 而是你决定了内存来自哪里 只需要调用构造函数 并在一个特定的内存地址中初始化你的Entity

```c++
int* d = new int[200];
Entity* e = new(d) Entity();
```

# 隐式构造函数 隐式转换

隐式 不会明确地告诉它要做什么 C++允许编译器对代码执行一次隐式转换 如果我们一开始有一个数据类型 然后有另一个类型 在两者之间 C++允许隐式进行转换 而不需要cast做强制转换 cast暂时不讨论 cast类型转换是将数据类型转换为另一个类型的过程

```c++
class Entity
{
private:
    std::string m_Name;
    int m_Age;
public:
    Entity(const std::string& name)
        : m_Name(name), m_Age(-1) {} //设置为-1 说明它是有效的
    
    Entity(int age)
        : m_Name("Unknown"), m_Age(age) {}
};

int main()
{
    Entity a("123"); // 姓名
    Entity b(22); // 年龄
    std::cin.get();
}
```

上面的一切都很正常 是我们平时做的 但如果你写

```c++
Entity a = "123";
Entity b = 22;
```

这就是隐式转换 或者隐式构造函数 隐式地将22转换成一个Entity 构造出一个Entity 

```c++
void PrintEntity(const Entity& entity)
{
    // print something
}

int main(){
    PrintEntity(22);
}
```

这也合法 因为C++认为22可以转换为一个Entity 调用`Entity(int age)`这个构造函数

```c++
int main()
{
    PrintEntity("123");
}
```

这不合法 因为"123"不是std::string 这是一个const char[4]数组

但你可以转换
`using namespace std::string_literals;` 然后写`PrintEntity("123"s);` 或者`PrintEntity(st::string("123"));`

或者写`PrintEntity(Entity("123"));`
`PrintEntity()`没有做隐式转换 只是把创建初始化Entity和执行函数放在了一起 但是`Entity("123")`做了隐式转换 将字符串转换成了std::string标准字符串

不会倾向于写成`Entity b = 22;` `PrintEntity(22);`这种感觉 因为看起来过于maigic 还是写成`Entity b(22);` `PrintEntity(Entity(22));`

**explict放在构造函数前面 意味着没有隐式转换 必须显式使用构造函数**

```c++
class Entity
{
private:
    std::string m_Name;
    int m_Age;
public:
    Entity(const std::string& name)
        : m_Name(name), m_Age(-1) {}
    
    explicit Entity(int age)
        : m_Name("Unknown"), m_Age(age) {}
};

int main()
{
    Entity a = "123";
    Entity b = 22; // 于是这个就不合法了
    std::cin.get();
}
```

隐式构造
`std::string`有一个接受`const char*`的构造函数 所以可以直接写 `std::string s = "";` 编译器会把`""`隐式转换成`std::string` 这是对象初始化的时候发生的
隐式转换
如果函数参数类型是`std::string` 传入`""`(C 风格字符串) 编译器会自动转换成 `std::string` 这是赋值的时候发生的

两者底层机制一样 都是编译器自动调用构造函数完成类型转换

# 运算符重载

运算符 代替函数做事的符号 不只是数学运算符
比如\*(逆向引用) -> += &(取地址) <<(cout的那个) new delete , () []

重载 给运算符重载赋予新的含义 或者添加参数 或者创建 允许在程序中定义或更改运算符的行为 运算符应该减少使用重载 只应该在完全有意义的情况下

运算符就是函数 不用给出函数名 只需要符号

```c++
struct Vector2
{
    float x, y;
    
    Vector2(float x, float y)
        : x(x), y(y) {}
    
    Vector2 Add(const Vector2& other) const
    {
    	return Vector2(x*other.x, y*other.y);
}

	Vector2 operator+(const Vector2& other) const
    {
    	return Add(other);
}
    
    Vector2 Multiply(const Vector2& other) const
    {
        return Vector2(x*other.x, y*other.y);
    }
    
    Vector2 operator*(const Vector2& other) const
    {
    	return Multiply(other);
	}
    
//     bool operator==(const Vector2& other1, const Vector2& other2)
//    {
//         return other1.x==other2.x && other1.y==other2.y;
//     }
// 我自己最开始写成了上面这样 但这显然根本不是一个类的方法的风格！ 只是函数 完全没习惯啊
	bool operator==(const Vector2& other)
    {
		return x==other.x && y==other.y;
	}
    
	bool operator!=(const Vector2& other)
    {
		return !(*this == other);
	}
    
};

std::ostream& operator<<(std::ostream& stream, const Vector2& other)
{
// 这是我们要重载的运算符<<的最初定义
// std::ostream& stream 接收的是std::cout
    stream << other.x << ", " << other.y;
    // other.x是浮点数 stream是知道如何打印浮点数的 所以不用对浮点数也进行重载
    return stream;
    // 要返回对stream的引用 因为流对象不可复制 必须使用引用传递
}

int main()
{
    Vector2 position(4.0f, 4.0f);
    Vector2 speed(0.5f, 1.5f);
    Vector2 powerup(1.1f, 1.1f); // 提升速度用
    
    Vector2 result1 = position.Add(speed.Multiply(powerup));
    Vector2 result2 = position + speed*powerup;
    // 这两个是一样的含义
    
    if(result1 == result2)
    {
        // do something
    }
    
    std::cout << result2 << std::endl;
}
```

也可以写成下面这样 只是代码风格的差异

```c++
Vector2 operator+(const Vector2& other) const
{
    return Vector2(x*other.x, y*other.y);
}

Vector2 Add(const Vector2& other) const
{
    return *this + other;
}
```

`*this` 关于this我们暂时先不讨论

this在本例中是一个const指针 逆向引用后就是一个Vector2对象 然后与other相加

```c++
std::cout << result2 << std::endl;
```

`<<`运算符 左边是cout类 右边是某种类型 直接这样写就不合法 `<<`运算符接收两个参数 一个是输出流 即cout 另一个是Vector2 这个运算符是不懂得如何打印Vector2类型的 所以必须重载

`stream << other.x << ", " << other.y;`
如果接收的stream是cout 就是逐个打印`other.x` `, ` `other.y` 从左到右依次处理每个<<操作

如果调用<<运算符
也就是`std::cout << result2 << std::endl;` 其中result2是一个Vector2
那么就是<<接收std::cout和result2为参数 按照重载之后的去做 即 逐个打印`result2.x` `, ` `result2.y` 最后再打印endl 即插入换行符\n 并刷新输出缓冲区

**最好的办法是把运算符和有相同功能的函数都实现出来 使用的人可以自行选择**

# this

this可以用于访问类的成员函数 或者叫方法 在方法内部 可以使用**this 是指向当前对象实例的指针** 该方法属于这个对象实例

```c++
class Entity
{
public:
    int x, y;
    
    Entity(int x, int y)
        : x(x), y(y)
};
```

如果不想用初始化列表 就会发现问题

```c++
void PrintEntity1(const Entity& e)
{
    // do something
}

void PrintEntity2(Entity* e)
{
    // do something
}

class Entity{
public:
    int x, y;
    
    Entity(int x, int y)
    {
        // x = x;
        // y = y;
        // 绝对没有办法像上面这样不明所以地写
        this->x = x;
        // 或者
        // (*this).x = x;
        this->y = y;
        
        PrintEntity1(this);
        PrintEntity2(*this);
        
    }
    
    int GetX() const
    {
        return x;
    }
};
```

this的类型就是`Entity*` 但如果鼠标悬停在this上 会发现它的类型是`Entity* const` const的意思是this是一个常量指针 指针指向的地址不会改变 但是指向的东西可以改变

如果想在类的内部调用一个类外部的函数 这个函数将Entity作为参数 就可以直接传入this

非const方法中 可以将this赋值给`Entity& e = *this` const方法中可以将this赋值给`const Entity& e = *this`

不要`delete this;` 这之后就再也不能访问类的成员数据

# 栈作用域生存期

进入一个作用域 就是在push栈帧 不一定非得是将数据push进栈帧 

if for while作用域 空{}作用域 类作用域

```c++
class Entity
{
private:
	int x;
};
```

当这个类消失时 变量也会消失

在作用域内栈创建类的实例对象 会调用构造函数 在`}`那行会调用析构函数

要避免[悬空指针](#mypoint_11)

作用域指针
是指针的包装器 在构造时用堆分配指针 在析构时删除指针

```c++
class ScopedPtr
{
private:
    Entity* m_Ptr;
public:
    ScopedPtr(Entity* ptr)
        : m_Ptr(ptr) {}
    ~ScopedPtr(){
        delete m_Ptr;
    }
};

int main()
{
    
    {
        // Entity* e = new Entity(); 原来是这样创建的 之后再手动删除
        // ScopedPtr e(new Entity()); 利用构造函数
        ScopedPtr e = new Entity();
        //这种是隐式转换写法 将Entity*对象转换为ScopedPtr对象 但是用这种写法就和之前看起来差不多
    }
    
}
```

只要离开作用域 e就会被销毁 因为实际上是在栈上分配的 new Entity()确实是在堆上分配 但是ScopedPtr的构造函数接收这个堆指针 又通过析构函数负责释放它

# 智能指针

可以取代new和delete

unique_ptr 因为不能复制unique_ptr 如果复制了就会有两个指针指向同一个内存块 如果有一个被销毁了 另一个就会变成指向已经释放了的内存

```c++
#include <memory>

class Entity
{
public:
    Entity()
    {
        //
    }
    
    ~Entity()
    {
        //
    }
    
    void Print()
    {
        //
    }
    
};

int main()
{
    
    {
        std::unique_ptr<Entity> e1(new Entity());
        std::unique_ptr<Entity> e2 = std::make_unique<Entity>();
        // 不能写
        // std::unique_ptr<Entity> e = new Entity();
        // 因为unique_ptr的构造函数是explicit 不能隐式转换
        // 不能使用Entity对象隐式构造一个std::unique_ptr<Entity>
        e2->Print();
        
    }
    
}
```

1. `std::unique_ptr<Entity> e1(new Entity());`
2. `std::unique_ptr<Entity> e2 = std::make_unique<Entity>();`

优先第二种写法 为了异常安全

这个智能指针就像一个普通的Entity型指针那样使用 作用域结束时 Entity会被自动销毁 这个智能指针只是一个栈分配对象 作用域结束它会自动调用delete

`std::unique_ptr<Entity> e0 = e1;` 智能指针不能复制 所以这样写就不合法

shared_ptr 引用计数 可以跟踪你的指针有多少个引用 一旦引用计数达到0 它就被删除了

- `std::shared_ptr<Entity> sharedE1 = std::make_shared<Entity>();`

<span id="mypoint_17"></span>`std::shared_ptr<Entity> sharedE2(new Entity());` 不能用这种写法 因为shared_ptr需要分配另一块内存 叫做控制块 用来存储引用计数 如果你已经分配好了一块new Entity 再传递给shared_ptr的构造函数 它就一共要做两次内存分配 先是new Entity的分配 又要分配shared_ptr的控制内存块 但如果用make_shared 就能把这两件事组合起来 而且**既然已经利用智能指针舍弃了new和delete 就不要再出现 但实际上它并没有真正取代new和delete**

shared_ptr可以复制 `std::shared_ptr<Entity> sharedE3 = sharedE2;`

weak_ptr 和shared_ptr一样可以复制 但是不会增加引用计数 比如你根本不想使用Entity 你只是在排序一个Entity列表 你不关心它们是否有效 只需要存储它们的一个引用

# 拷贝与拷贝构造函数

不必要的复制是不好的

```c++
int a = 2;

int b = a;
```

a和b是不同的内存 复制的是值 修改b之后 a不会发生改变 但如果a b是指针复制 就会影响 复制指针也只不过是在复制内存地址的数字 

引用是不能赋值的 只能一开始的时候初始化 所以只要写`=` 就是发生了赋值 复制了一遍

```c++
class String
{
    char* m_Buffer; // 指向字符缓冲区
    unsigned int m_Size;
public:
    String(const char* string){
        m_Size = strlen(string);
        m_Buffer = new char[m_Size+1];
        // 考虑空终止符 写char[m_Size+1]
        memcpy(m_Buffer, string, m_Size+1);
        // 将string的字符复制到m_Buffer
        // 也可以用for循环一个一个地复制
        // 如果不能保证string这个字符串有空终止符
        // 就要添加一句
        // m_Buffer[m_Size] = 0;
    }
    
    ~String()
    {
        delete[] m_Buffer;
    }
    
    char& operator[](unsigned int index)
    {
        return m_Buffer[index];
    }
    
    friend std::ostream& operator<<(std::ostream& stream, const String& string);
    // 把声明复制过来就可以写成友元
};

std::ostream& operator<<(std::ostream& stream, const String& string)
{
    // stream << string.GetBuffer();
    // 这样就需要写一个GetBuffer的方法
    // 可以把这个重载的运算符变成友元
    stream << string.m_Buffer;
    return stream;
}

int main()
{
    String string = "123";
    String second = string; // 在这里调用了拷贝构造函数
    
    std::cout << string << std::endl;
    std::cout << second << std::endl;
    
    std::cin.get();
}
```

`String second = string;` 这一句是复制这个String 实际上就是将所有类成员变量`char*`和`m_Size`复制到一个新的内存地址 就是String second 现在内存中有两个String 它们进行了复制 这种复制称为**浅拷贝** 是复制了指针`char*` 这两个内存 有着相同的`char*`值 因此你修改一个的值 另一个也会跟着一起变化 到达作用域结束时 String会被销毁 那么析构函数就要delete两次m_Buffer 两次释放同一个内存块 程序会崩溃

真正我们需要分配一个新的char数组 来存储复制的字符串 现在我们只是复制了指针 就需要**深拷贝**

浅拷贝不会去到指针的内容或者指针所指向的地方 也不会去复制它 深拷贝是会复制整个对象

我们使用拷贝构造函数 C++会默认提供一个拷贝构造函数 

默认拷贝构造函数 可以直接在类里写

```c++
String(const String& other);
```

如果把默认拷贝构造函数的功能自己实现出来就是

```c++
String(const String& other)
    : m_Buffer(other.m_Buffer), m_Size(other.m_Size) {}
```

或者写成

```c++
String(const String& other)
{
    memcpy(this, &other, sizeof(String));
}
```

但是用默认的不行 因为我们不仅想复制指针 我们想复制指针所指向的内存

如果决定不需要拷贝构造函数 不允许复制 就写

```c++
String(const String& other) = delete;
```

这里是和unique_str不允许复制的内部实现很相似

这样之后 我们之前在主函数里写的`String second = string;`就不能编译了 所以**之前我们在这个语句中 当时就是用了默认拷贝构造函数**

```c++
String(const String& other)
    : m_Size(other.m_Size)
{
    m_Buffer = new char[m_Size+1];
    memcpy(m_Buffer, other.m_Buffer, m_Size+1);
}
```

回顾一下我们的构造函数

```c++
String(const char* string)
{
    m_Size = strlen(string);
    m_Buffer = new char[m_Size+1];
    memcpy(m_Buffer, string, m_Size+1);
}
```

**构造函数是从零开始构造 拷贝构造函数是用来拷贝其他对象的**

- 构造函数传`const char*` 从一个原始C字符串开始创建 它可能是一个指向任意长度字符串的指针 我要用strlen计算它的长度 再用memcpy将原始字符串内容复制到新分配的内存中 这是属于深拷贝
  `String s = "Hello";`
- 拷贝构造函数传`String&` 我已经知道这是一个String 它内部有存储size 不用再计算 直接使用other这个String实例自带的m_Size 然后深拷贝 复制other.m_Buffer的全部内容 包括\0

**如果写函数直接传String类型 而不是传引用的话 也会调用拷贝构造函数 所以应该传const引用**

```c++
void PrintString(const String& string)
{
	//do something
}
```

**无论如何 对于String 无论是你自己写的字符串类 还是std::string 优先传const引用 不要复制**

# -> 箭头操作符

```c++
Entity e;
e.Print();

Entity* ptr = &e;
// ptr.Print(); 不能这样写
```

ptr只是一个指针 一个数值 不是对象 不能调用方法

```c++
(*ptr).Print();
ptr->Print();
// 这两种写法是等效的
```

可以重载

```c++
// 手写智能指针
class ScopedPtr
{
private:
    Entity* m_Obj;
public:
    ScopedPtr(Entity* entity)
        : m_Obj(entity) {}
    
    ~ScopedPtr()
    {
        delete m_Obj;
    }
    
    Entity* operator->()
    {
        return m_Obj;
    }
    
    // 也需要写一个const版本
    // 后续创建e3时使用了这个版本
    const Entity* operator->() const
    {
        return m_Obj;
    }
    
};

int main()
{
    Entity* e1 = new Entity();
    e1->Print();
    // 如果不用智能指针 就是像上面那样写
    // 但如果用自己写的智能指针 就要重载运算符->
    ScopedPtr e2 = new Entity();
    e2->Print();
    
    const ScopedPtr e3 = new Entity();
    e3->Print();

    std::cin.get();
}
```

使用-> 获取内存中某个成员变量的偏移量

```c++
struct Vector3
{
    float x, y, z;
};
```

每一个float有4个字节 所以x的偏移量是0 y的偏移量是4 z是8 但如果你不知道类内部的变量顺序 就不知道偏移量了

```c++
int offset = (int)&(((Vector3*)0)->x);

// (Vector3*)nullptr：将空指针nullptr强制转换为Vector3*类型指针 此时指针值为0
// ->x：访问该指针指向的Vector3对象的成员变量x
// &(...->x)：获取成员变量x的地址
// (int)：将地址转换为整数类型
```

这里nullptr也可以写成0
nullptr只能用于表示空指针 不能表示空整数或其他类型 它的设计初衷是解决0作为空指针时的类型歧义问题

最后计算出来x的偏移量是0

空指针的地址被假设为0 成员变量x的地址=空指针地址(0)+x在Vector3中的偏移量
即 &(nullptr->x) = 0 + offset_of(x)

# vector

```c++
struct Vertex
{
    float x, y, z;
};

std::ostream& operator<<(std::ostream& stream, const Vertex& v)
{
    stream << v.x << ", " << v.y << ", " << v.z;
    return stream;
}

int main()
{
    Vertex vertices_stack[5];
	Vertex* vertices_heap = new Vertex[5]; 
	// 无论是栈创建还是堆创建 都要指定具体的大小

    std::cin.get();
}
```

我们需要一种方式 在到达最大容量时 重新调整容量

```c++
#include <vector>

int main()
{
    std::vector<Vertex> vertices;

    std::cin.get();
}
```

也可以在`std::vector<?????>` 指定成原始类型 比如int

存储vector对象比存储指针在技术上更优 vector对象的内存分配是线性的 是内存连续的数组 这样再去操作会很容易 因为都在同一个cache line上 **优先存储对象**

唯一的问题是 如果要调整单个vector的大小 就要复制所有的数据 会比较缓慢 而如果是指针 实际的内存保持不变 因为你只是保存了一系列指向内存的指针 调整大小的时候 数据仍然存储着 当vector需要扩容时 它会分配一块更大的连续内存 并将原有的指针值（即内存地址）复制到新内存中 指针指向的实际对象不会被复制或移动 它们仍驻留在原有的内存位置 而由于指针的大小固定 只取决于你的系统是多少位的 复制速度极快 扩容开销低

```c++
std::vector<Vertex> vertices;
vertices.push_back({ 1, 2, 3 });
vertices.push_back({ 4, 5, 6 });
vertices.push_back({ 7, 8, 9 });

for (int i = 0; i < vertices.size(); i++)
    std::cout << vertices[i] << std::endl;
	// []运算符已经重载了 现在就像普通数组一样
```

现在就会输出

```c++
1, 2, 3
4, 5, 6
7, 8, 9
```

<span id="mypoint_14"></span>也可以使用 **for循环的语法糖**

```c++
for (Vertex v : vertices)
	// 遍历vertices的所有元素 将当前元素拷贝构造到临时变量v中 其实就是复制
	std::cout << v << std::endl;
```

但我们要尽可能避免复制 传引用

```c++
for (Vertex& v : vertices)
    // 更可以用const Vertex&
	std::cout << v << std::endl;
```

将数组大小设回为0

```c++
vertices.clear();
```

如果想移除数组的特定元素 比如第3个元素 也就是索引为2的那个元素

```c++
vertices.erase(vertices.begin() + 2);
```

再对vertices数组进行输出 就会输出

```c++
1, 2, 3
4, 5, 6
```

成功地删除了第3个元素

**将vector传给函数或者类或者什么其它东西的时候 要确保是用引用传递 如果只读就用常量引用**

```c++
void Function(const std::vector<Vertex>& vertices)
{
    // do something
}

int main()
{
    std::vector<Vertex> vertices;
    vertices.push_back({ 1, 2, 3 });
	vertices.push_back({ 4, 5, 6 });
	vertices.push_back({ 7, 8, 9 });
    
	Function(vertices);
    
    std::cin.get();
}
```

## std::vector使用优化

你创建一个vector 然后你开始push_back元素 也就是向数组中添加元素 如果vector的容量不够大 不能容纳你想添加的新元素 vector就需要扩容 将内存中旧位置的所有内容复制到内存中的新位置 然后删除旧位置的内存 每次容量用完都要调整大小重新分配 有很多不必要的复制 如何避免

**只需要设置拷贝构造函数 你就会知道到底发生了多少次复制**

```c++
struct Vertex
{
    float x, y, z;
    
    Vertex(float x, float y, float z)
        : x(x), y(y), z(z) {}
    
    Vertex(const Vertex& other)
        : x(vertex.x), y(vertex.y), z(vertex.z)
    {
		std::cout << "Copied!" << std::endl;
    }
};

int main()
{
    std::vector<Vertex> vertices;
    vertices.push_back({ 1, 2, 3 });
    vertices.push_back({ 4, 5, 6 });
    vertices.push_back({ 7, 8, 9 });
	// 写成vertices.push_back(Vertex(1, 2, 3)); 会更易读
	// 这样就是调用了Vertex的构造函数 创建临时Vertex对象传入push_back中
    // 而不再是隐式构造
    
    std::cin.get();
}
```

会输出6次Copied!

1. `std::vector<Vertex> vertices;`
   vertices对象本身是存储在main函数的栈帧中 此时这个vector size=0 capacity=0
   
2. `vertices.push_back({1, 2, 3})` 
   用**聚合初始化隐式构造**一个临时Vertex对象{1, 2, 3} 当然也可以用`vertices.push_back(Vertex(1, 2, 3));`显式构造 无论显式还是隐式构造 都是调用了Vertex的构造函数  最后要把临时对象从栈帧拷贝到真实的那个Vector所在的内存中 实际上是**在main函数的栈帧中构造了这个临时Vertex对象** push_back尝试将这个临时对象添加到vector中 而vector初始为空 容量为0 就需要扩容 **vector的元素是存储在堆内存中 与main栈帧无关** 所以要分配堆内存 容量为1 然后**将main栈帧中的临时对象拷贝构造到vector的堆内存中** 触发拷贝构造函数 输出一个Copied! **main栈帧中的临时对象在表达式结束之后销毁**
   此时 vector size=1 capacity=1
   
3. `vertices.push_back({4, 5, 6})`
   隐式构造第二个临时Vertex对象{4, 5, 6} 当前vector容量为1 但需要存储2个元素 需要**扩容** 新容量为2\*capacity=2 **将原有元素从旧的堆内存拷贝构造到新的堆内存** 输出一个Copied! 将新临时对象{4, 5, 6}从main栈帧拷贝构造到新的堆内存 输出一个Copied! 然后销毁旧内存中的元素 此时vector size=2 capacity=2
   
4. `vertices.push_back({ 7, 8, 9 });` 现在vector的容量是2 再添加{7, 8, 9}就需要扩容 会扩容成4 {1, 2, 3}从旧内存复制到新内存是调用1次拷贝构造函数 {4, 5, 6}从旧内存复制到新内存是调用1次拷贝构造函数 {7, 8, 9}从临时对象复制到新内存是调用1次拷贝构造函数 此时vector size=2 capacity=2

debug模式下 把鼠标悬停在vertices变量名上 再按小三角▶ 就可以看到size、capacity、vector中的元素列表 可以显示每个vector对象的具体值

然而拷贝次数太多了 如何优化？

减少扩容次数？ 比如你大概知道你要用多少内存 创建一个那样大的vector就好了 避免扩容 防止反反复复地从旧的堆内存复制到新的堆内存

```c++
std::vector<Vertex> vertices;
vertices.reserve(3);
```

这和`std::vector<Vertex> vertices(3);`是有区别的

1. `vertices.reserve(3);` 分配足够容纳3个Vertex对象的未初始化堆内存 仅分配内存 所以不依赖构造函数 size仍为0 capacity变为3

2. `std::vector<Vertex> vertices(3);` 是调用std::vector的构造函数重载 构造一个包含3个默认初始化的Vertex对象的vector 因为要默认初始化 这就需要Vertex类有默认构造函数 但我们写的Vertex类没有默认构造函数

   ````c++
   Vertex()
   {
       // 里面写点什么 或者什么都不写
   }
   ````

   只有需要参数的构造函数
   ```c++
   Vertex(float x, float y, float z) : x(x), y(y), z(z) {}
   ```
   所以就无法通过编译了 如果有默认构造函数 就会size变为3 capacity变为3 其实我们根本不需要创建对象 只是希望开辟足够的内存

添加了reserve之后 就只会有3次Copied 因为不需要扩容

但我们仍然在 将临时对象从main栈帧复制到实际的vector中 还在复制 还在复制

于是我们不再使用push_back 而是emplace_back 这时候就不能传`Vertex(1, 2, 3)` 不能`vertices.emplace_back(Vertex(1, 2, 3));` 因为不能传我们已经构建的Vertex对象 而是`vertices.emplace_back({ 1, 2, 3 });` 只传Vertex构造函数的参数列表 告诉vector 用下列参数直接在实际的vector内存中构造一个Vertex对象

```c++
std::vector<Vertex> vertices;
vertices.reserve(3);
vertices.emplace_back({ 1, 2, 3 });
vertices.emplace_back({ 4, 5, 6 });
vertices.emplace_back({ 7, 8, 9 });
```

现在这样就没有任何复制发生 输出0个Copied

# C++库

倾向于在实际解决方案的项目文件夹中 保留使用的库的版本 从源码构建 因为有助于调试 或者可以修改库 而不是使用包管理器 但如果想快速使用 就选择预构建的二进制文件

暂时先不考虑获取实际依赖库的源码自己编译 先考虑如何链接二进制文件 

glfw库

在[官网](https://www.glfw.org/)就可以下载Windows pre-compiled binaries 但是下载32位二进制(32-bit)还是64位 不=和你实际的操作系统没有关系 取决于你在开发什么目标应用程序 你的解决方案是要在哪个配置之下 x86还是x64 如果不匹配 就无法进行链接

现在我们下载64位的 解压缩打开看到

```c++
docs // 官方文档
include // 头文件 GLFW/glfw3.h 和 GLFW/glfw3native.h
lib-mingw-w64 // 为 MinGW-w64 编译器预编译的库文件
lib-static-ucrt // 稍后介绍
lib-vc2013
lib-vc2015
lib-vc2017
lib-vc2019
lib-vc2022 // 为 Visual Studio 2022 编译的 动态库
LICENSE.md
README.md
```

这是C++库的典型文件组织结构 有不同编译器编译出来的库文件 mingw-w64和很多版本的visual studio

库通常有两部分 includes(包含目录)和library(库目录)

includes是一堆头文件 这样我们就可以实际使用预构建的二进制文件中的函数

lib中有那些预构建的二进制文件 分为静态库和动态库 但也不是所有的库都会提供这两种库 可能只有一种 但是glfw提供了两种 你可以选择静态链接还是动态链接

在解决方案文件夹里 创建名为dependencies的文件夹 依赖项 也就是库文件的目录 在这个文件夹里 创建一个名为GLFW的文件夹 把GLFW库的include和lib-vc2022文件夹复制到这里 打开lib-vc2022文件夹

静态链接意味着 这个库会被放到你的可执行文件中 它在你的exe文件中 所有代码都被编译进你的程序

动态链接是运行时链接 是一个单独的文件 在运行时你需要把它放到你的exe文件旁边 或者其它某个地方 然后你的exe文件可以加载它

意思就是 如果我只依赖静态库写程序 发布给别人 我只需要给别人这个exe文件就好了 他就可以直接使用 但是如果我依赖了动态库写程序 我想要发布给别人使用 我不仅要给他这个exe文件 我还必须把我依赖的动态库放在旁边提供给他 或者我就要求他的设备本身就拥有这个动态库

静态链接会更快 编译器或者链接器可以执行链接时优化 但是动态库就必须保持它的完整 没办法优化 动态链接库被运行的程序装载时 程序的部分将被补充完整 所以静态链接是更好的选择

xxxxxxx.dll 动态库本体 需要随程序分发
xxxxxxxdll.lib 导入库 包含了对应的.dll中所有函数、符号的位置 所以可以在编译时链接它们 如果没有.lib 仍然可以使用.dll
xxxxxxx.lib 静态库 明显占据的空间更大

假如我正在自己写库 无论我写了动态库还是静态库 总之现在我这个库依赖了动态库
比如

你编写了一个静态库mylib.lib 并让它依赖了动态库dependency.dll 也就是说 你的库在代码中调用了dependency.dll中的函数 那么用户在使用你的库mylib.lib时
编译期间 用户需要链接dependency.lib（动态库的导入库）
运行期间 用户必须在手头有dependency.dll 否则程序会崩溃

**你希望用户完全无需处理dependency.dll的问题 唯一的解决方案就是将这个依赖库也静态链接** 也就是把dependency.dll替换成静态库版本dependency.lib 这样用户在编译时就只需要链接你的这个库 不用再处理dependency.dll的事情 代价是 你的静态库体积增大了 这是你需要取舍的

这其实也就是lib-static-ucrt所做的事情

lib-static-ucrt这个文件夹里 包含文件
glfw3.dll 动态库本体
glfw3dll.lib 动态库的导入库（用于链接）

于是我们可以判定 这是一个动态库 那么 为什么它的名字里有static 这是因为 lib-static-ucrt是一个 静态链接了ucrt运行时库 的 动态库

首先解释 什么是运行时库？

运行时库（Runtime Library）是编译器提供的基础函数库 所有程序都需要它们 你的程序在运行时必须依赖这些库才能正常工作 它们包含了许多核心功能 比如 malloc free printf fopen strcpy strlen 等等

ucrt就是一个Win10引入的通用C运行时库（ucrtbase.dll） 所以Win7自然是没有这个东西的 为了程序兼容性 我们就需要把ucrt这个库 即ucrtbase.dll 静态链接到glfw3.dll这个动态库中 这样用户就可以在旧系统上仍能使用glfw库

所以 尽管目录名包含static 但它实际提供的是动态库dll 只是将运行时库ucrt以静态方式链接在其中了

因为ucrt是一个运行时库 它太基本了 你只有两种选择
要么是动态链接运行时库 这就要求用户的设备里必须有ucrtbase.dll win10之后的系统里都有 你不用担心
要么是静态链接运行时库 将运行时库的代码直接打包到你的程序中 这样即使是用户在比win10更旧的系统里 也可以使用你的程序 代价是程序占据的空间变大

而假如 无论我写了一个静态库还是动态库 总之我这个库 依赖了静态库 其他人在使用我的库时 不仅需要下载我的库 还需要下载我依赖的那个库

所以 假如我写库 无论是静态库还是动态库 也无论我依赖了静态库还是动态库 只要其它人使用我的**库** 他就必须也同时拥有我依赖的那个库 如果我希望我的用户避免再去处理依赖库的问题 我的唯一解决方案就是把我依赖的库 静态链接到我写的库里

而静态库和动态库的唯一区别 是用户在发布使用这个库开发的**程序**的区别 仅依赖静态库开发的程序 在分发时不需要再提供单独的库文件 只需要发布可执行文件exe 而依赖了动态库开发的程序 在分发时也要同时发布单独的动态库文件 否则你就必须指望用户的系统里已经存在这个动态库

优先动态链接的场景 依赖库更新频繁 目标系统较新
优先静态链接的场景 依赖库稳定且体积较小（如数学库） 需要兼容旧系统

打开解决方案 右键项目 点击属性 先把配置换成所有配置 所有平台 然后点C/C++ - 常规 - 附加包含目录 也就是include文件夹的路径 **最好写相对路径** 

在[Visual Studio设置](#Visual Studio设置) 我们似乎做过类似的工作 

解决方案所在的目录为 \$(SolutionDir) 先把它输入进去 再点击附加包含目录最右侧的小三角箭头 再点编辑 可以看到计算的值为`D:\coding\C++\Project_test\` 也可以点击宏 在列表中找到 \$(SolutionDir)
而此前我们存放include的目录为 `D:\coding\C++\Project__test\dependencies\GLFW\include` 

可以双击左侧文本框进行修改 最后填入的是`$(SolutionDir)dependencies\GLFW\include` 在计算的值那栏也可以实时看到地址 你还可以发现这里已经有了一个`%(AdditionalIncludeDirectories)` 这是当前已有的附加包含目录 也就是父级（如全局、平台、配置等）已经设置的目录

\$(SolutionDir) 指的是解决方案.sln所在目录
\$(ProjectDir) 指的是项目文件.vcxproj所在目录

```c++
#include "GLFW/glfw3.h"
// 因为glfw3.h是在D:\coding\C++\Project__test\dependencies\GLFW\include\GLFW文件夹里
```

Windows 默认使用 反斜杠 \ 作为路径分隔符 例如 C:\Program Files\GLFW\include 但现代 Windows 系统也支持 正斜杠 / 例如 C:/Program Files/GLFW/include
Unix/Linux/macOS 统一使用 正斜杠 / 作为路径分隔符 例如 /usr/local/include/GLFW

**全都优先使用 正斜杠 / 跨平台**

<span id="mypoint_12"></span> ***< > 和 " " 的区别***

`#include <header.h>`  
编译器优先在 系统级包含目录 和 显式指定的外部依赖目录 中搜索头文件

1. 系统级目录：如 `C:\Program Files (x86)\Microsoft Visual Studio\...\include`（Windows）
2. 用户通过编译器参数显式指定的目录（如 `-I/path/to/external`）  
3. **不搜索当前文件所在目录**

`#include "header.h"`  
编译器按以下顺序搜索：  

1. 当前文件所在目录（包含相对路径） 
2. 项目内显式指定的目录（如 Visual Studio 的项目属性中配置的包含路径）  
3. 系统级包含目录 和 外部依赖目录

**如果头文件在Visual Studio中 在解决方案中的某个地方 无论是不是在同一个项目里 但同属一个解决方案 就使用""**
**如果是一个完全的外部依赖 外部的库 不在Visual Studio中和我的实际解决方案一起编译 那就用<> 表明它是外部的 然后通过项目属性中设置附加包含目录来让编译器找到它 所以可以通过设置附加包含目录来同时使用多个头文件**

目前 解决方案.sln 是在`D:\coding\C++\Project_test`文件夹
我的main.cpp在 `D:\coding\C++\Project__test\Project__test\src`
而我要用的头文件glfw3.h 在`D:\coding\C++\Project_test\dependencies\GLFW\include\GLFW`

1. <span id="mypoint_13"></span>第一种方法 我可以设置项目的包含路径 `$(SolutionDir)dependencies\GLFW\include`
   那么我就可以写头文件 `#include <GLFW/glfw3.h>` 表示是显式配置的外部路径 这个头文件是通过设置附加包含目录找到的 而不是通过" "去查找相对路径找到的
   但其实这个头文件就在我们的解决方案里 所以也可以写 `#include "GLFW/glfw3.h"` 表示这个头文件就在解决方案内部 是我们的源文件之一 而不是来自解决方案外部
   其实用这两种写法都可以 但规范更倾向于
   **第三方库写< >**
   **自研库写" " 但也不用相对路径 仍然是配置附加包含目录后写简短路径**

2. 第二种方法 假如我这个glfw未必就和我的解决方案放在一起 那我就重新把包含路径设置成glfw当前所在的位置 可以写绝对路径 也可以设置环境变量 然后写`#include <GLFW/glfw3.h>` 表明它是外部的 没和我的解决方案在一起 也属于依靠显式设置的外部路径来找寻头文件

3. 第三种方法 我不设置项目的附加包含目录 我就写`#include "../../dependencies/GLFW/include/GLFW/glfw3.h"` " "会搜索当前目录的相对路径 但是是相对main.cpp的路径 因为我现在是要在main.cpp里使用这个头文件 这种方法要求库和解决方案基本是放在一起的

所以 在我们当前设置了包含路径为`$(SolutionDir)dependencies\GLFW\include` 的情况下 以下两种写法都可以 是一模一样的

```c++
#include "../../dependencies/GLFW/include/GLFW/glfw3.h"
#include <GLFW/glfw3.h>
```

鼠标悬停在`<GLFW/glfw3.h>`上面 当然悬停在`"../../dependencies/GLFW/include/GLFW/glfw3.h"`上面也可以 按ctrl 就可以直达头文件glfw3.h的内容 当然也可以右键 - 转到文档 是一样的

```c++
#include <iostream>
#include <GLFW/glfw3.h>

int main()
{
	int a = glfwInit();
    std::cin.get();
}
```

现在生成这个项目 就会报错 无法解析的外部符号 说明我们没有链接到真正的库

glfwInit 鼠标悬停在glfwInit()上 ctrl并点击 就可以看到在glfw3.h中 `GLFWAPI int glfwInit(void);` 只有一个声明 告诉我们这个函数存在 但没有函数体 所以就不能成功链接

如果我们在main.cpp中实现这个函数

```c++
#include <iostream>
#include <GLFW/glfw3.h>

int glfwInit()
{
	return 0;
}

int main()
{
	int a = glfwInit();
    std::cin.get();
}
```

现在就可以重新生成 得到了Project_test.exe 但我们不想用自己写的这个 想用库里面的那个 把自己写的这个函数删掉

.lib和.dll都是二进制文件 所以看不到内部函数的具体实现 除非用反汇编工具

## 静态链接

右键项目 - 属性 - 链接器 - 输入 - 附加依赖项 编辑填入`glfw3.lib`
在链接器 - 常规 - 附加库目录 编辑填入`$(SolutionDir)dependencies\GLFW\lib-vc2022`

现在已经指定了库目录 也指定了库文件的名称 现在就可以成功生成了 a的值最后是1

```c++
#include <iostream>
// #include <GLFW/glfw3.h>
// 将头文件删除掉了

extern "C" int glfwInit();
// 自己写了一个声明

int main()
{
	int a = glfwInit();
	std::cout << "GLFW initialized: " << a << std::endl;
	std::cin.get();
}
```

头文件删除了 但头文件能提供的也就只有函数声明 而我自己写了一个声明 所以不再需要头文件 编译器也能知道glfwInit是存在的 在编译时它就自动搜索项目依赖的库文件 来找到glfwInit的二进制实现

C++支持函数重载 编译器会对函数名进行修饰 使用签名 比如glfwInit可能被编译为_Z8glfwInitv 来区分不同参数类型的同名函数 而GLFW是使用C编写的库 函数名在这个库里就是glfwInit `extern "C"`就是告诉编译器 这个函数使用C的链接规则 不要对函数名进行修饰 这样链接器就可以找到GLFW库中的函数实现

头文件提供声明 告诉我们哪些函数是可用的
库文件提供函数定义 这样就可以链接到具体的函数

## 动态链接

对于动态库 有两种形式

1. 静态的 动态库版本 我已经知道里面有什么函数 我可以使用什么
2. 任意加载这个动态库 甚至不知道里面有什么

GLFW同时支持静态库与动态库 头文件的使用方式仍然是`#include <GLFW/glfw3.h>`

右键项目 - 属性 - C/C++ - 常规 我们的附加包含目录仍然和静态链接一样

属性 - 链接器 - 输入 - 附加依赖项 **静态链接中我们写入的是glfw3.lib 动态链接中我们要写入动态库的导入库 glfw3dll.lib**

现在 生成项目会报错 找不到glfw3.dll 所以现在要复制dll 把dll和可执行文件exe放在一起  就可以正常使用了 可执行文件的目录是一种自动搜索路径

查看这个glfw3.h 发现2000多行才出现第一个函数声明 在此之前全都是宏定义#define typedef一类的东西

```c++
GLFWAPI int glfwInit(void);
```

悬停在GLFWAPI上 并没有看到什么东西 不如右键查找所有引用 或者转到定义 速览定义 就可以看到它的#define

GLFWAPI宏 用于修饰GLFW的公共API函数
在构建 GLFW 库时 标记函数需要导出 暴露给其他程序使用
在使用 GLFW 库时 标记函数需要导入 从库中加载实现

```c++
/* GLFWAPI is used to declare public API functions for export
 * from the DLL / shared library / dynamic library.
 */

#if defined(_WIN32) && defined(_GLFW_BUILD_DLL)
 /* We are building GLFW as a Win32 DLL */
// 在 Windows (_WIN32) 且正在 构建 GLFW 为 DLL (_GLFW_BUILD_DLL)
 #define GLFWAPI __declspec(dllexport)
// __declspec(dllexport) 告诉编译器：导出此函数 使其可在 DLL 外部调用

#elif defined(_WIN32) && defined(GLFW_DLL)
 /* We are calling a GLFW Win32 DLL */
// 在 Windows (_WIN32) 且 用户代码通过 DLL 使用 GLFW (GLFW_DLL)
 #define GLFWAPI __declspec(dllimport)
// __declspec(dllimport) 告诉编译器：此函数从 DLL 导入 优化调用效率

#elif defined(__GNUC__) && defined(_GLFW_BUILD_DLL)
 /* We are building GLFW as a Unix shared library */
// 使用 GCC/Clang (__GNUC__) 且正在 构建 GLFW 为共享库 (_GLFW_BUILD_DLL)
 #define GLFWAPI __attribute__((visibility("default")))
// visibility("default") 强制函数在共享库中可见（默认情况下 GCC 会隐藏符号）

#else
// 静态链接或非动态库场景
 #define GLFWAPI
// GLFWAPI 定义为空 函数使用普通声明（无特殊导出/导入逻辑）
#endif
```

`#if defined(_WIN32) && defined(_GLFW_BUILD_DLL)`

怎么知道`_WIN32` `__GNUC__` `_GLFW_BUILD_DLL` `GLFW_DLL` 是否defined？

当编译器目标平台是Windows时 Windows平台编译器自动定义`_WIN32`
`_GLFW_BUILD_DLL` 从源代码用cmake编译 且选择构建为动态库时 定义的
`GLFW_DLL` 是要用户调用这个库时手动定义的 

```c++
#define GLFW_DLL  // 必须在包含 glfw3.h 前定义！
#include <GLFW/glfw3.h>
```

**通过宏封装差异 使 GLFW 的 API 在所有平台上保持统一** 体现了 C/C++ 底层开发的精髓 通过预编译机制抽象平台差异 为用户提供简洁一致的接口

但我在visual studio中 并没有 `#define GLFW_DLL` 也成功使用了动态库 但是没有优化 没有 `__declspec(dllimport)` 会导致函数调用多一次跳转 性能损失约 5-10%

现在是因为我闲着没事才查看了GLFWAPI的定义 我知道需要GLFW_DLL 但如果是其它第三方库 我怎么知道还要定义宏才能优化性能？

阅读官方文档 例如GLFW文档明确说明 `On Windows, define GLFW_DLL to use the GLFW DLL.` 或者查看头文件

悬停在glfwInit上 发现可以看到函数功能描述和参数介绍 这是因为使用了**Doxygen风格的注释** 只要写在头文件或源文件的函数声明/定义前 IDE就能识别

```c++
/*!
 * @brief 计算两个整数的和
 * @param a 第一个整数
 * @param b 第二个整数
 * @return 两数之和
 */
int add(int a, int b);
```

`/*! ... */` `/** ... */` Doxygen支持的注释块
`@brief` 描述函数
`@param` 参数
`@return` 返回值

## 创建库和使用库

现在我们已经有了名为创建一个名为Game的解决方案 它自带一个名为Game的空项目

在这个解决方案里再创建一个名为Engine的空项目

右键Game项目 属性 - 常规 - 配置类型 设置成 应用程序.exe
右键Engine项目 属性 - 常规 - 配置类型 设置成 静态库.lib
应用到 所有配置 所有平台

按照[Visual Studio设置](#visual-studio设置)修改输出目录和中间目录 以及创建src文件夹

解决方案视图
在Game项目 右键源文件 通过 新建项 创建 Application.cpp
在Engine项目 分别右键源文件和头文件 创建 Engine.h和Engine.cpp
再都分别移动到src文件夹中

也可以先在文件夹视图 src文件夹中都通过新建项创建好 再切回解决方案视图 右键源文件或者头文件 添加 - 现有项 选择src文件夹里那些 这样就把文件都组织到了项目之中

```c++
// Engine.h
#pragma once

namespace engine
{
	void PrintMessage();
}
```

头文件里不需要实现这个函数

```c++
// Engine.cpp

#include "Engine.h"

#include <iostream>

namespace engine
{
	void PrintMessage()
	{
		std::cout << "Hello from the Engine!" << std::endl;
	}
}
```

```c++
// Application.cpp

#include "../../Engine/src/Engine.h"
// 根据""会搜索相对目录这样写

int main()
{
	engine::PrintMessage();
}
```

也可以通过项目属性设置

右键Game项目 -  属性 - C/C++ - 常规 -  附加包含目录 写入`$(SolutionDir)Engine\src`

现在就可以写头文件 `#include "Engine.h"` 其实[前面](#mypoint_13)已经讨论过了

现在对Engine项目进行生成 我们得到了一个Engine.lib 按照之前设置好的输出目录和中间目录 它应该在 `D:\coding\C++\Game\bin\x64\Debug` Visual Studio的输出窗口在生成结束后 其实已经为你输出了它的所在地址

右键Game项目 - 链接器 - 输入 - 附加依赖项 写入Engine.lib
链接器 - 常规 - 附加库目录 写入 `$(SolutionDir)bin\x64\Debug`
按照之前静态链接的方法 我们应该是像这样做

但是 这个lib是在我们的解决方案之中
右键Game项目 - 添加 - 引用 - 项目 - 解决方案 选择这个Engine项目
现在 就和我们手动把lib文件添加到链接器中一样

引用的好处是如果我们修改了库的名字 仍然可以使用 而不用麻烦地修改

现在Game依赖于Engine 所以如果Engine发生了修改 我们去编译Game 编译Game实际上就是Game和Engine都编译了 所以即使你忘记了编译Engine也无所谓

右键Engine项目 清理 这样生成的.lib文件就没有了 现在直接生成Game 在输出窗口就可以看到 先生成了项目Engine 又生成了项目Game 因为Game引用了Engine Game需要Engine才能工作

将Application.cpp修改为

```c++
#include "Engine.h"

#include <iostream>

int main()
{
	engine::PrintMessage();
	std::cin.get();
}
```

这样就不会马上退出程序 运行程序 就可以看到控制台确实输出了Hello from the Engine!

我们在 `D:\coding\C++\Game\bin\x64\Debug` 找到我们的Game.exe 将它复制到桌面上 点击运行 没有任何问题！ 这就是静态库 不需要外部文件依赖

# 多返回值

C++默认情况下 不能返回多种类型 python可以 因为它在这背后做了很多事情

```c++
// 引用
static void ParseShader(const std::string& filepath, std::string& vertexSource, std::string& fragmentSource)
{
	// 中间做了一些事情
    
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    vertexSource = vs;
    fragmentSource = fs;
    // 总之是更新了vertexSource和fragmentSource
}

int main()
{
    std::string vs, fs;
    // 栈创建
	ParseShader("res/shaders/Basic/shader", vs, fs);
}
```

因为**传的是引用** 直接传地址也是一样的效果 所以函数执行结束之后 vs fs都更新了 **就相当于有多个返回值**

``` c++
// 数组 指针
static std::string* ParseShader(const std::string& filepath)
{
    // do something
    
    return new std::string[] { vs, fs };
    // 堆分配
}
```

返回的是数组 其实是一个指针 我们不知道这个数组有多大

```c++
// 数组 std::array 只有多返回值的类型相同时才有用
// 用std::vector也行 但array会在栈上创建 而vector是在堆上
// 因此返回std::array会更快

static std::array<std::string, 2> ParseShader(const std::string& filepath)
{
    // do something
    // return std::array<std::string, 2>(vs, fs);
    // 如果你不清楚std::array的语法 就用
	std::array<std::string, 2> results;
    results[0] = vs;
    results[1] = fs;
    return results;
    
}
```

下面是通用方法 可以返回不同类型的变量

tuple

```c++
#include <tuple>
static std::tuple<std::string, std::string> ParseShader(const std::string& filepath)
{
    // do something
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    return std::make_pair(vs, fs);
    // 这样就会返回tuple
}
```

调用时可以用`std::tuple<std::string, std::string> sources = ParseShader("某个地址");` 或者直接 `auto sources = ParseShader("某个地址");`

从tuple里取数据 要用`std::get<0>(sources)`
0是索引值 所以这里取出来的是vs 我们无法从get这里直接看到取出来的元素的类型 只知道它的索引值 虽然我们早就知道vs是什么类型 但这个数字还是过于magic了

```c++
static std::pair<std::string, std::string> ParseShader(const std::string& filepath)
{
        // do something
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    return std::make_pair(vs, fs);
}
```

调用时可以用`std::get` 但也可以用`sources.first` `sources.second` 得到的分别是vs fs 但还是不知道每个元素的变量类型

<span id="mypoint_18"></span>所以终极方式是**struct结构体**

```c++
struct ShaderProgramSource
{
    std::string VertexSource;
    std::string FragmentSource;
};

static ShaderProgramSource ParseShader(const std::string& filepath)
{
    // do something
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    return { vs, fs };
}
```

调用时用 `sources.VertexSource, sources.FragmentSource` 这样就比较清楚

# 模板 template

其它语言里大概叫泛型

```c++
void Print(int value)
{
    std::cout << value << std::endl;
}

void Print(std::string value)
{
    std::cout << value << std::endl;
}
```

太重复 要重构很多次 换一个数据类型就要写一次

```c++
template<typename T>
// 也可以写成template<classname T>
void Print(T value)
{
	std::cout << value << std::endl;
}
```

模板并不是一个真正的函数 **只有实际调用时 这些函数才被真的创建** 所以就算模板里的函数应该是会报错的 比如有语法错误 它也不会报错 只有被调用后 还会报错

```c++
Print(5);
Print("Hello");
Print(5.5);
```

类型是隐式地从实际参数中得到 可以自动推导出T是什么 也可以写成 `Print<int>(5);`

```c++
class Array
{
private:
    int m_Array[size];
}
```

我们希望创建在栈上创建一个C风格的数组 但不知道size 但是这个size只有在编译时才会知道 模板正是在编译时才被补全

```c++
// 不再是typename 而是已知size就是一个int
template<int N>
class Array
{
private:
    int m_Array[N];
public:
    int GetSize() const { return N; }
};

int main()
{
    Array<5> array;
    std::cout << array.GetSize() << std::endl;
    std::cin.get();
}
```

如果<>可以重载成[] 一定会看起来很美观

调用之后**编译器为你生成的代码**就是

```c++
class Array
{
private:
    int m_Array[5];
public:
    int GetSize() const { return 5; }
};
```

类型为T的数组


```c++
template<typename T, int N>
class Array
{
private:
    T m_Array[N];
public:
    int GetSize() const { return N; }
};

int main()
{
    Array<int, 5> array;
    std::cout << array.GetSize() << std::endl;
    std::cin.get();
}
```

实际上C++标准库也是 `std::array<int, 5> arr;`

适度使用模板

# 堆与栈

栈和堆是ram(主内存)中实际存在的两个区域 栈通常是一个预定义大小的内存区域 约2兆字节左右 堆也是一个预定义了默认值的区域 却可以生长 它可以随着应用程序的进行而改变

比如我们创建一个int 一般的系统都是4个字节 我们要请求内存分配一个由4个字节内存组成的连续块 连续的意思是在一行中 

```c++
struct Vector3
{
    float x, y, z;
    
    Vector3()
        : x(10), y(11), z(12) {};
};

int main()
{
    int value = 5;
	// 栈分配

    int array[5];
    // 栈分配
    array[0] = 1;
    array[1] = 2;
    array[2] = 3;
    array[3] = 4;
    array[4] = 5;

    Vector3 vector;
    // 栈分配



    int* hvalue = new int;
    *hvalue = 5;
    // 堆分配

    int* harray = new int[5];
    // 堆分配
    harray[0] = 1;
    harray[1] = 2;
    harray[2] = 3;
    harray[3] = 4;
    harray[4] = 5;

    Vector3* hvector = new Vector3();
    // 也可以写成
    // Vector3* hvector = new Vector3;
    // 堆分配
    
    delete hvalue;
    delete[] harray;
    delete hvector;
}
```

设置断点 查看&value 在栈分配那些全执行完之后

```c++
05 00 00 00 cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc
cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc 01 00 00 00 02 00
00 00 03 00 00 00 04 00 00 00 05 00 00 00 cc cc cc cc cc cc cc
cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc cc
00 00 20 41 00 00 30 41 00 00 40 41 cc cc cc cc cc cc cc cc cc
```

value是在低地址 array在高地址 vector的地址更高

最后那个Vector3的存储是浮点数 会有这么多cccccc在填充 只是因为debug模式下的安全守卫 在变量周围 防止溢出

分配多少字节内存 就是栈指针要移动多少字节 内存是互相叠加存储的 就像栈 现在栈的实现都是倒着来的 向下增长的 比如

```c++
int value = 5;
int array[5];
```

先一次性为value+array分配24个字节 (1+5)*4=24

```c++
高地址
| array[4] |  ← 后声明的（高地址）
| array[3] |
| array[2] |
| array[1] |
| array[0] |
|----------|
| value=5  |  ← 先声明的（低地址）
低地址     ← 当前栈顶rsp
```

**按声明顺序从低地址向高地址填充 先声明的变量地址更低 这是编译器优化的结果 栈是从高地址向低地址增长 所以栈顶是在低地址** 低地址就是内存地址在数字上更小的那个

查看堆分配的地址 hvalue和harray完全没有存储在一起

在堆上分配内存请查看[new](#new) 是一系列的事情 而在栈上分配内存就只类似于一条指令 所以栈分配会更快 这是它们最主要的差别 查看反汇编就可以看到差异

# 宏

不要过度使用宏

`#` 预编译指令符号

```c++
#define WAIT std::cin.get()
#define OPEN_CURLY

int main()
OPEN_CURLY
    WAIT;
}
```

可以但没必要 也可以把`;`放在宏里

```c++
#define LOG(x) std::cout << x << std::endl

int main()
{
    LOG("Hello");
    std::cin.get();
}
```

debug下我们想用日志系统 但release下 对于我们的用户 输出到控制台的日志系统是没有必要的 而且还要额外耗时 于是我们就需要**在release版本中去掉所有的日志代码 但又要在debug版本中保留 可以通过宏实现**

右键项目 - 属性

debug配置下 C/C++ - 预处理器 - 预处理器定义 编辑写入`PR_DEBUG` PR来自于我们这个项目Project_test的缩写 比如你的项目是Sparky游戏引擎 你可以写`SP_DEBUG` 总之这是你自己的宏 不会和其它的宏冲突

release配置下 在这里编辑写入`PR_RELEASE` 本例中我们不会用到这个

```c++
#ifdef PR_DEBUG
// 如果定义了PR_DEBUG
#define LOG(x) std::cout << x << std::endl
#else
// 否则
#define LOG(x)
#endif

int main()
{
    LOG("Hello");
    std::cin.get();
}
```

Visual Studio如果选择了Debug模式下查看 就发现`#define LOG(x)`这一行是暗淡的 切换到Release模式 `#define LOG(x) std::cout << x << std::endl`这一行变得暗淡

这段代码成功地在debug模式下输出hello 在release模式下什么都不输出
但这段代码还可以优化 不倾向于使用`#ifdef`

```c++
// 原来是 #ifdef PR_DEBUG
// 仅仅只是定义 还不够好
#define PR_DEBUG 1
// 可以通过修改这里是1还是0 来决定是否使用日志系统
// 也可以不在这里写定义 转而去属性设置里
// 将PR_DEBUG修改成PR_DEBUG=1（不能有空格）
// 就可以把上面这行代码去掉了

#if PR_DEBUG == 1
#define LOG(x) std::cout << x << std::endl
#else
// 这句#else 也可以改成 #elif defined(PR_RELEASE)
#define LOG(x)
#endif
```

可以在这段代码前后加上

```c++
#if 0
// 中间的那些宏代码 全部都会被折叠
#endif
```

宏必须都写在同一行 但 `\`是换行符的转义 `\`后不要有空格 那样就会变成是对空格的转义 而不是换行

```c++
#define MAIN int main() \
{\
	std::cin.get();\
}

MAIN
// 替换了
// int main()
// {
//     std::cin.get();
// }
```

# auto

让C++自动推导出数据类型

```c++
int a = 5;

auto b = a;
```

鼠标悬停在b上 看到的是int b

```c++
auto a = 5; // int
auto a = 5L; // long
auto a = 5.5f; // float
auto a = "abc"; // const char*
```

仿佛C++变成了不那么关心类型的弱类型语言 只需要到处写auto就行了 是否到处都只用auto 取决于编程风格

```c++
std::string GetName()
{
	return "abc";
}

int main()
{
    auto name = GetName();
    
    std::cin.get();
}
```

如果改变GetName的返回类型 主函数里也什么都不用变 也就是说改变了API 客户端也什么都不用变 我们甚至都不知道API已经改变了 但也会因此使得依赖于特定类型的代码失效

个人倾向于减少使用auto 因为希望清楚地知道变量的类型 读代码的时候看到auto并不能知道是什么变量类型 除非鼠标悬停

```c++
std::vector<std::string> strings;
string.push_back("Apple");
string.push_back("Orange");

// 迭代器
// 也可以用for each / for range 那个C++11语法糖
for (std::vector<std::string>::iterator it = strings.begin(); it != strings.end(); it++)
{
    std::cout << *it << std::endl;
}
```

`std::vector<std::string>::iterator` 这个东西 迭代器 基本上就是一个指针 它指向容器中的特定元素 vector list map set容器都可以用迭代器 这里容器是vector 元素的数据类型是`std::string` 它的名字是`it` 于是我们可以用`*it`对其逆向引用 来读取或者修改它指向元素的值

可以直接把`std::vector<std::string>::iterator`换成`auto` 提升代码的可读性

现在迭代器不太常用 推荐用[for range](#mypoint_14)

```c++
for (const std::string& str : strings)
{
	std::cout << str << std::endl;
}
```

或者直接 `for (const auto& str : strings)`

```c++
class Device {};

class DeviceManager
{
private:
    std::unordered_map<std::string, std::vector<Device*>> m_Devices;
    // 从string到vector<Device*>的映射 名称为m_Devices
public:
    const std::unordered_map<std::string, std::vector<Device*>> GetDevices() const
    {
        return m_Devices;
    }
}

int main()
{
    using DeviceMap = std::unordered_map<std::string, std::vector<Device*>>;
    // 给过于漫长的类型 起个别名
    // 可以把这个using放在类里
    // 也可以用
    // typedef std::unordered_map<std::string, std::vector<Device*>> DeviceMap;
    
    DeviceManager dm;
    const DeviceMap& devices = dm.GetDevices();
    
    std::cin.get();
}
```

实际上这里非常适合使用auto

```c++
DeviceManager dm;
const auto& devices = dm.GetDevices();
// 如果直接用auto devices = dm.GetDevices(); 就会产生一次复制
```

类型名字很长的时候 可以考虑用auto 但我个人还是宁愿用using 也不想用auto

# std::array

静态数组 不增长的数组 不能改变它的大小

```c++
#include <array>

int main()
{
    std::array<int, 5> data;
    data[0] = 2;
    data[4] = 1;
    
    int dataOld[5];
    // 旧的C风格数组
        
    std::cin.get();
}
```

基本上只是声明方式有那么一些差别

```c++
void PrintArray(int* array, unsigned int size)
// 数组在传递时会退化为指针 不带有大小信息
// 为了循环 要传入数组的大小 现在需要追踪两个变量了
{
    for (int i = 0; i < size; i++)
    {
        // print
    }
}

void PrintArrray(const std::array<int, 5>& data)
// 结果还是在std::array<int, 5> 传了数组大小
{
    for (int i = 0; i < data.size(); i++)
    {
        // print
    }
}
```

有没有不用传数组大小的办法

```c++
// 原始数组 使用模板
template <size_t N>
void PrintArray(int (&array)[N])
// 引用 (&array)[N] 防止数组退化成指针
// 如果我不用模板 比如只想接受大小为5的数组
// 就可以 void PrintArray(int (&array)[5])
// 其实array这里换个名字也可以
// 比如void PrintArray(int (&b)[5])
{
    for (int i = 0; i < N; i++)
    {
        // print
    }
}

// 调用示例
int arr[] = {1, 2, 3, 4, 5};
PrintArray(arr);  // 自动推导 N=5
```

```c++
// std::array
template <size_t N>
void PrintArray(const std::array<int, N>& data) {
    for (int i = 0; i < data.size(); i++) {
        // print
    }
}

// 或者用for range
template <size_t N>
void PrintArray(const std::array<int, N>& data) {
    for (const auto& item : data) {
        // print
    }
}

// 调用示例
std::array<int, 5> data = {1, 2, 3, 4, 5};
PrintArray(data);  // 自动推导 N=5
```

最优速度下 效率和原始数组没有区别
`.size()` 是`std::array`的一个优势 size是一个模板参数 并不存在什么存储在数组中的size变量
作为迭代器 也有`.begin()` `.end()`
这个类也可以用大量的STL(标准模板库)算法 因为它支持迭代器

和原始数组一样 都是栈创建 而不像vector是堆分配

点击array头文件查看其源代码 这个头文件就是我们要看的 我们不需要看它是怎样实现的

忽略那些宏 `_`开头的也是宏 我们可以看到模板类

```c++
template <class _Ty, size_t _Size>
class _Array_const_iterator { ... };
// 常量迭代器

template <class _Ty, size_t _Size>
class _Array_iterator { ... };
// 非常量迭代器

_EXPORT_STD template <class _Ty, size_t _Size>
class array { ... };
// array主模板

template <class _Ty>
class array<_Ty, 0> { ... };
// 针对 size=0 的特化版本array
```

展开`class array` 可以看到 fill、swap、begin()、end()、size()、empty()、at()(返回索引处的元素并进行强边界检查 有检查开销)、operator[]

```c++
_CONSTEXPR20 void fill(const _Ty& _Value) {
// 批量赋值
// 接收一个 _Value 参数（类型与数组元素相同）
// 将数组中所有 _Size 个元素设置为 _Value
// 等效于：for (auto& elem : arr) elem = value;
    _STD fill_n(_Elems, _Size, _Value);
}

_CONSTEXPR20 void swap(array& _Other) noexcept(_Is_nothrow_swappable<_Ty>::value) {
// 交换两个同类型数组的全部内容
    _STD _Swap_ranges_unchecked(_Elems, _Elems + _Size, _Other._Elems);
}

_NODISCARD _CONSTEXPR17 iterator begin() noexcept {
    return iterator(_Elems, 0);
}

_NODISCARD _CONSTEXPR17 const_iterator begin() const noexcept {
    return const_iterator(_Elems, 0);
}

_NODISCARD _CONSTEXPR17 iterator end() noexcept {
    return iterator(_Elems, _Size);
}

_NODISCARD _CONSTEXPR17 const_iterator end() const noexcept {
	return const_iterator(_Elems, _Size);
}

_NODISCARD _Ret_range_(==, _Size) constexpr size_type size() const noexcept {
// 这是一个源代码注解(SAL) 用于静态代码分析工具 Microsoft特有的
// _Ret_range_： 表示注解的对象是函数的返回值范围
// (==, _Size)： 指定返回值必须严格等于符号 _Size 的值
    return _Size; // 返回模板参数_size
}

_NODISCARD constexpr size_type max_size() const noexcept {
    return _Size;
}

_NODISCARD _CONSTEXPR17 reference operator[](_In_range_(<, _Size) size_type _Pos) noexcept /* strengthened */ {
#if _MSVC_STL_HARDENING_ARRAY || _ITERATOR_DEBUG_LEVEL != 0
	_STL_VERIFY(_Pos < _Size, "array subscript out of range");
    // 条件边界检查 仅调试/强化模式检查
#endif

	return _Elems[_Pos]; // 返回这个索引上元素的引用 以便之后的读和写
}
```

# 函数指针

之前我们都是调用函数 但是我们没有把函数作为参数传递给其它函数

```c++
void HelloWorld()
{
	std::cout << "Hello World!" << std::endl;
}

int main()
{
	HelloWorld(); // 平时我们都这么用
    auto myHelloWorld = &HelloWorld;
    
    myHelloWorld();
    myHelloWorld();
    
    std::cin.get();
}

// 会输出3个 Hello World!
```

`auto myHelloWorld = &HelloWorld;` 没有用`HelloWorld()` 这样就不是在调用函数 而是在**获取函数指针** 把函数指针赋值给了function 我们得到了这个函数的内存地址 然后赋值给了funtion
函数只是cpu指令 编译代码时 函数就在二进制文件的某个地方 暂时我们先不钻研二进制文件 想象当你编译你的代码时 每个函数都被编译成cpu指令 它就在我们的二进制文件中 在我们的可执行文件中 所以`&HelloWorld`的意思就是 在可执行文件中找到这个helloworld函数 获取那些cpu指令的内存地址
可以直接写`auto myHelloWorld = HelloWorld;` 会发生一个隐式转换 直接将函数名赋值给指针时 C++会自动把它当作是地址

这里auto的类型是 `void(*)()` 是指向 无参数且返回void的函数 的指针
函数指针的声明语法是`返回类型 (*指针变量名)(参数类型)` myHelloWorld是变量名 如果不用auto就是 `void (*myHelloWorld)() = HelloWorld;` `()`是空的因为HelloWorld这个函数没有参数
还是使用auto或者using/typedef别名吧

```c++
// 方法1
auto myHelloWorld = HelloWorld;

// 方法2
void (*myHelloWorld)() = HelloWorld;

// (最佳)方法3
using myFunctionType = void(*)();
myFunctionType myHelloWorld = HelloWorld;

// 方法4
typedef void(*myFunctionPtr)();
myFunctionPtr myHelloWorld = HelloWorld;
```

`typedef 返回类型 (*新类型名)(参数类型);`
结构和函数指针声明的`返回类型 (*指针变量名)(参数类型)` 非常相似
还是用using更好

```c++
void HelloWorld(int a)
{
	std::cout << "Hello World! Value: " << a << std::endl;
}

int main()
{
    void(*myHelloWorld int a) = HelloWorld;
    // using myFunctionType = void(*)(int a);
    // myFunctionType myHelloWorld = HelloWorld;
    
    myHelloWorld(1);
    
    std::cin.get();
}

// 会输出 Hello World! Value: 1
```

所以为什么要使用函数指针？

```c++
void PrintValue(int value)
{
    std::cout << "Value: " << value <<std::endl;
}

void ForEach(const std::vector<int>& values, void(*func)(int))
// 希望在这个函数里调用某个函数 本例中将会调用PrintValue
{
    for (int value : values)
    {
        func(value);
    }
}

int main()
{
	std::vector<int> values = { 1, 5, 2, 4, 3};
    ForEach(values, PrintValue);
    // 传入了名为values的vector
    // 然后对这个vector中的每一个元素 都执行PrintValue函数
    
    std::cin.get();
}
```

其实PrintValue这么一点信息 根本不用写成函数 特别是我们只想在ForEach内部使用 这就可以使用[lambda](#mypoint_15) 其实就是一个匿名函数 只是不像普通函数这样声明

```c++
ForEach(values, [](int value){std::cout << "Value: " << value <<std::endl;});
```

完全可以直接这样写

这种C原始的函数指针真的很古老 几乎不用

可以考虑`std::function`

```c++
#include <functional>
#include <vector>

void ForEach(const std::vector<int>& values, std::function<void(int)> func) {
    for (int value : values) {
        func(value);
    }
}

ForEach(values, [](int x) { std::cout << x; }); // Lambda表达式
ForEach(values, &PrintValue);                   // 函数指针
```

`std::function<void(int)> func` 就是接收int参数的 返回void类型的 名为func的 函数指针 和`void(*func)(int)`差不多

## 回调

函数指针是为了在一个函数中调用另一个函数才做的传参

1. 假如是在一个类的内部 **类成员函数之间相互调用** 就不需要用函数指针传参 甚至也不用考虑声明顺序 直接调用就可以了 暂时我们不讨论成员函数指针
2. C语言是声明在后面的函数就可以直接调用声明在前面的函数 所以有时候**调整声明顺序**就行了 即使是 前面函数 的实现 用到了后面的函数 只需要把后面函数的声明写到 前面的函数 前面就可以了 这也属于调整声明顺序
3. 调用其它文件里的函数的场合 是用头文件 头文件中声明函数 源文件中实现函数 在要调用这个函数的文件中写头文件 不需要函数指针 直接调用

这几种 在一个函数里调用另一个函数 的场合 全都不需要使用函数指针 将一个函数作为参数传给另一个函数 那么什么时候是必要的？

- 动态选择算法（运行时决策）

```c++
// 根据不同条件选择不同处理函数
void ProcessData(int mode, const std::vector<int>& data) {
    void (*processor)(int) = nullptr;
    
    // 根据模式动态选择处理函数
    if (mode == 1) processor = &ProcessMode1;
    else if (mode == 2) processor = &ProcessMode2;
    else processor = &DefaultProcess;
    
    // 使用选择的函数处理数据
    for (int value : data) {
        processor(value);
    }
}
```

这样写是为了更简化 **防止写重复代码** 就就不用一遍一遍重复地 在每一个if-else分支里都写for循环 然后又在for循环内部分别使用不同的函数

- 回调机制（事件驱动编程）

```c++
// GUI按钮点击回调
class Button {
public:
    void setOnClick(void (*callback)()) {
        onClickHandler = callback;
    }
    
    void click() {
        if(onClickHandler) onClickHandler();
    }
    
private:
    void (*onClickHandler)() = nullptr;
};

// 使用
Button saveButton;
saveButton.setOnClick(&saveFile); // 设置回调函数
```

回调(Callback)是一种编程模式 它允许我们将一个函数作为参数传递给另一个函数 然后在某个特定事件发生时调用这个传递进来的函数

大概就是游戏存档吧 saveFile是一个函数 传给了setOnClick Button 类将这个传入的函数指针存储在私有成员变量onClickHandler中 于是onClickHandler函数就变成了saveFile函数 非空了
这之后只要用户发生了点击保存按钮的行为 也就是saveButton.click() saveButton调用了click函数 就能成功调用onClickHandler函数了 实际上是在调用saveFile函数 发生保存成文件的行为
我们不知道用户什么时候会点击 于是我们设置它点击后会发生什么行为

常规思路应该是 给save按钮专门写一个saveClick的函数 用户点击按钮即为发生saveClick事件 直接通过saveClick函数调用saveFile函数函数 但是按钮不止有一种 这样的话我们就要写很多种click函数 很麻烦
还有一个最根本的问题 为什么非要写什么saveClick()或者是什么click() 很麻烦 反正saveClick里也无非就是调用了saveFile函数 不如直接让saveButton调用saveFile函数 `saveButton.saveFile()`
但是这样的话 由于saveButton是Button类的一个实例 它要调用函数的话 在Button类的内部就要实现saveFile函数 但实际上Button类只是一个按钮类 它没有必要知道到底是怎么保存文件存档的 而且Button并不只有saveButton这一种 如果全是这种思路的话 Button类中就要写非常多的实际上和Button没什么关系的功能实现函数

所以最后我们写了通用的`setOnClick` 把某个函数传入给setOnClick 这样就会设置好了 在用户点击按钮发生事件时 就会执行我们设置好的函数 **Button类不需要知道这是一个什么函数 更不知道这个函数具体怎么实现 它只知道 它提前接受了一个地址 这是一个函数的地址 它设置好了这个函数 它不知道用户什么时候按下按钮 但它知道当用户发生点击按钮的事件时 就执行这个函数**

实际上这个的思路就和动态选择算法(运行时决策)是一样的 都是把有差异化的部分提前处理好了 最后写成一个统一的东西 防止分情况讨论 写大量的重复代码

也可以有更多功能

```c++
Button autoSaveBtn;

// 根据难度设置不同的存档策略
if (difficulty == EASY) {
    autoSaveBtn.setOnClick(&quickSave);
} else {
    autoSaveBtn.setOnClick(&fullSave);
}
```

```c++
// 云存档版本
void cloudSave() { /* 保存到云端 */ }

// 本地存档版本
void localSave() { /* 保存到本地 */ }

// 根据玩家设置选择 useCloudSave时玩家提前设置好的
if (useCloudSave) {
    saveBtn.setOnClick(&cloudSave);
} else {
    saveBtn.setOnClick(&localSave);
}
```

```c++
template<typename T>
class UltimateButton {
public:
    using ActionType = std::function<void(T)>;
    
    void setAction(ActionType action) {
        m_action = action;
    }
    
    void click(T arg) {
        if(m_action) m_action(arg);
    }

private:
    ActionType m_action; // 私有的类成员变量
};

// 使用示例1：无参数按钮
UltimateButton<void> saveBtn;
saveBtn.setAction([] { saveGame(); });

// 使用示例2：带参数按钮
UltimateButton<int> volumeBtn;
volumeBtn.setAction([](int level) { setVolume(level); });

// 使用示例3：复杂对象
struct Player { string name; int health; };
UltimateButton<Player> healBtn;
healBtn.setAction([](Player& p) { p.health = 100; });

// 所有按钮共享同一个实现类！
```

- 写通用算法框架

```c++
// 通用数组处理函数
template<typename T>
void TransformArray(T* array, size_t size, T (*transformFunc)(T)) {
    for(size_t i = 0; i < size; ++i) {
        array[i] = transformFunc(array[i]);
    }
}

// 使用
double square(double x) { return x * x; }
double cube(double x) { return x * x * x; }

double data[100];
TransformArray(data, 100, &square); // 平方处理
TransformArray(data, 100, &cube);   // 立方处理
```

- 插件系统/动态加载

```c++
// 动态加载库中的函数

// 共享库文件：通常是.so文件（Windows上是.dll）
// dlopen()：打开共享库的函数
// dlsym()：从打开的库中获取符号（函数或变量）地址
// dlclose()：关闭库

void* library = dlopen("plugin.so", RTLD_LAZY);
// RTLD_LAZY 表示 懒加载 即在需要时才解析符号
// 返回的是void*类型的库句柄 相当于打开库的钥匙
if (library) {
// 如果成功打开
    // 获取函数指针
    auto pluginFunc = (void(*)(int))dlsym(library, "plugin_function");
    // 在库中查找名为plugin_function的函数
    // 利用(void(*)(int)) 将dlsym返回的void*转换为 接收int参数 返回void 的函数指针
    
    if (pluginFunc) {
        pluginFunc(42); // 尝试调用插件函数
    }
}
```

# labmda

<span id="mypoint_16"></span>只要有一个函数指针 就可以在C++中使用lambda 这是不需要通过函数定义就可以定义一个函数的方法

```c++
void ForEach(const std::vector<int>& values, void(*func)(int))
{
    // do something
}

int main()
{
    // ForEach(values, [](int value){std::cout << "Value: " << value <<std::endl;});

    // 也可以写
    auto lambda = [](int value){std::cout << "Value: " << value <<std::endl;}
    ForEach(values, lambda);
}
```

这是函数指针时我们使用的lambda

```c++
#include <functional>

void ForEach(const std::vector<int>& values, std::function<void(int)>& func)
{
    // do something
}

int main()
{
	int a = 5;
	
	auto lambda = [&a](int value){std::cout << a << value <<std::endl;}
    // 将 a 引用传入 lambda
    // 但是无论是写[=][&][a][&a]
    // 下面这个ForEach的lambda处都会报错
    // 所以要把ForEach从原始函数指针 修改成std::function
	
    ForEach(values, lambda);
}
```

```c++
#include <algorithm>

std::vector<int> values = { 1, 5, 2, 4, 3 };

auto it = std::find_if(values.begin(), values.end(), [](int value) { return value>3; }) 
std::cout << *it << std::endl;
```

`find_if()` 函数前两个参数接收容器的迭代器 用于确定查找的范围 第三个参数是一个规则函数 **查找范围内的数据将会逐个传递给这个规则函数** 所以这个规则函数必然有一个参数是和容器里的元素同样类型的 规则函数最终会返回一个bool值 如果返回true 就表示现在这个数据是符合规则函数的条件的 那么find_if会返回指向现在这个数据的迭代器 如果返回false 意思就是不符合规则函数中的条件 规则函数会接收下一个数据 继续开始判断 如果到达查找范围结束时 还没不符合条件 就返回指向查找范围末尾的迭代器

# 命名空间 namespace

什么时候使用namespace？

如果写`using namespace std;` 就不用写`std::`了 可以放全局 也可以只放在某个函数里 可以在任何作用域里使用

如果是命名空间名字很长 或者有自己的命名空间 项目文件中的符号全都在这个命名空间中 需要经常访问调用那些命名空间中的符号 这时候可能会想要使用命名空间 但是 不喜欢using namespace std

因为去掉了`std::` 会看起来不明不白 你分不清哪些是C++标准库的 哪些是原始C的 非常不舒适 很难读

**永远不要在头文件中使用using namespace** 这样别人使用你的头文件 就相当于把你写的use namespace复制到了自己代码的最开头 它是全局的 导致别人后面的代码直接没办法写了

如果一定要using namespace 建议只using自己亲手在本地写的库 并且要在足够小的作用域里使用 比如if语句内部 函数内部 尽量不要全局

Pascal命名法 每个单词首字母大写 中间没有空格和下划线 常用于类名 接口名

驼峰命名法 第一个单词小写 从第二个单词开始首字母大写 常用于变量名 函数名

C++标准库是喜欢 都小写单词 中间用下划线连接

```c++
namespace apple {
    void print(const std::string& text)
    {
        std::cout << text << std::endl;
    }
}

namespace orange {
    void print(const char* text)
    {
        // 倒转字符串 打印
        std::string temp = text;
        std::reverse(temp.begin(), temp.end());
        std::cout << temp << std::endl;
    }
}

using namespace apple;
using namespace orange;

int main()
{
    print("Hello");
}
```

现在它会调用orange 为什么？

"Hello"是一个const char[6]的数组 不是string 如果没有orange 在apple的print里 就会做一个隐式转换 把const char数组转换成string 但是现在有一个直接就能接收const char的orange 所以调用orange

这属于runtime  error 不是complied error

如果两个都接收const char 就会无法通过编译

报错信息里所说的“符号” 指的是 类 函数 变量 常数 有两个相同符号 就会链接错误 如果两个符号在同一个文件里 就会编译错误

glfw库 是C语言的库 兼容C语言和C++ 因为是C库 所以不能使用命名空间 所以函数名是GLFWInit GLFWCreateWindow这种形式 C语言的函数名写法就会是apple_print orange_print

命名空间主要的目的就是避免命名冲突 

```c++
namespace apple { namespace functions {
// 这样写缩进就可以清楚地看到有几层命名空间
// 而且函数也不需要再缩进了
    
}
    
}
```

类本身就是命名空间 

# 线程

```c++
#include <thread>

static bool s_Finished = false;

void DoWork()
{
    using namespace std::literals::chrono_literals;

    std::cout << "thread id=" << std::this_thread::get_id() << std::endl;

    // 另一个执行线程中我们希望它做的事
    while (!s_Finished)
    {
        std::cout << "Working...\n";
        std::this_thread::sleep_for(1s); // sleep 1秒
    }
}

int main()
{
    std::thread worker(DoWork); // 需要接收函数指针
    // 这句代码结束之后 它就立即启动那个线程

    std::cin.get(); // 那个线程在持续打印 但当前线程在始终等待我们按下enter
    s_Finished = true; // 这样就打断那个线程

    worker.join(); // 在当前线程上等待这个线程完成它的工作 确保线程实际上真的完成了
    std::cout << "Finished." << std::endl;
    std::cout << "thread id=" << std::this_thread::get_id() << std::endl;

    std::cin.get();
}
```

持续输出working 但我们又希望能随时等待用户按下enter就打断输出 所以下面这样就完全不对 `cin.get()`是会阻塞整个线程的

```c++
// 完全不对的
while (true)
{
    std::cout << "Working...\n";
    std::cin.get();
}
```

但是那个线程输出的太快了 导致这个线程的cpu使用率达到100% 这不是很好 可以让那个线程sleep一会

thread join 线程加入 我们暂时不讨论了 其它语言中它常常叫做 wait / wait for exit

调用join的目的是 在主线程上等待工作线程完成所有的执行之后 再继续执行主线程

`using namespace std::literals::chrono_literals;` 字面量 这样就可以直接写3s直接表示3秒 3ms表示3毫秒 3h表示3小时

`std::this_thread`可以用于给当前线程下命令

输出结果是

```c++
Start thread id=3932
Working...
Working...
Working...
Working...

Finished.
id=10904
```

可以看到两个线程的id是不一样的

# 计时

想看程序用了多长时间

C++库 chrono 不需要操作系统库

```c++
#include <iostream>
#include <chrono>
#include <thread>

int main()
{
    using namespace std::literals::chrono_literals;

    auto start = std::chrono::high_resolution_clock::now(); // 当前时间
    std::this_thread::sleep_for(1s);
    auto end = std::chrono::high_resolution_clock::now(); // 当前时间

    std::chrono::duration<float> duration = end - start;
    std::cout << duration.count() << "s" << std::endl;


    std::cin.get();
}
```

最后是输出了1.0079s

```c++
#include <iostream>
#include <chrono>
#include <thread>

struct Timer
{
    std::chrono::steady_clock::time_point start, end;
    std::chrono::duration<float> duration;

    Timer()
    {
        start = std::chrono::high_resolution_clock::now();
    }

    ~Timer()
    // 依赖析构函数制作计时器
    {
        end = std::chrono::high_resolution_clock::now();
        duration = end - start;

        float ms = duration.count() * 1000.0f; // 想使用毫秒更精确
        // duration.count() 将duration从原来的类型 转换成了folat类型
        std::cout << "Time took " << ms << "ms" << std::endl;
    }
};

void Function()
{
    Timer timer; // 作用域结束后它会自动析构

    for (int i = 0; i < 100; i++)
        std::cout << "Hello" << std::endl;
}

int main()
{
    Function();

    std::cin.get();
}
```

使用对象生存期 让它为我自动计时

Hello打印了100次 耗时18.9916ms

`std::endl`是非常慢的 比起`\n` 它额外做了刷新缓冲区 可以换成`std::cout << "Hello\n"` 变成耗时5.5381ms
何时使用 `std::endl`？比如日志、错误信息这类需要实时显示输出的 或者交互式程序 要确保用户及时看到提示 或者多线程调试 避免输出因缓冲区延迟混淆顺序 其他情况下 优先使用 `\n` 以提升性能

也可以用IDE的分析工具 暂时不讨论

# 多维数组

二维数组就是数组的数组 想象一个指针的数组 最后会得到一个内存块 里面包含的是连续的指针 每个指针都指向内存中的某个数组 所以我们得到的是指向数组的指针的集合 也就是数组的数组

`int**` 指向指针集合的指针 这是一个指向int指针的指针

我们现在在构建64位程序 64位程序的所有地址都是64位的 所以所有类型的指针都应该是64位的 而int是32位的 `int*`是指针型 存储的是int的地址数字 是64位的 `int**`也是指针型 存储的是`int*`型的地址数字 是64位的

`int* array = new int[50];` 分配50个指针 就是50\*64位 50\*8个字节

```c++
int* array = new int[50]; // 200个字节
int** a2d = new int* [50]; // 400个字节
```

现在只是分别分配了200字节和400字节内存而已 没有初始化任何东西

```c++
a2d[0] = nullptr; // a2d[0]是int指针
array[0] = 0; // array[0]是int
```

类型只是一种语法 设置类型是用来处理数据的

我们现在已经存储了400个字节的指针 50个指针 我们可以遍历并设置每个指针指向一个数组 这样我们就得到了一个包含50个数组的内存位置的数组

```c++
int** a2d = new int* [50];

for (int i = 0; i < 50; i++)
    a2d[i] = new int[50];
    // 这是50*50的二维数组
```

三维数组就用嵌套的for循环

```c++
// 50*50*50的三维数组
int*** a3d = new int**[50];
// 分配了50个 指针的指针

for (int i = 0; i < 50; i++) {
    a3d[i] = new int*[50]; // 让这个指针的指针 指向一个int型指针数组
    
    for (int j = 0; j < 50; j++) {
        // 现在 i可以认为是一个常数
        a3d[i][j] = new int[50]; // 让这个指针指向一个int数组
        // int** ptr = a3d[i]; // a3d[i]是指针的指针 它指向一个int型指针数组 赋值给了ptr ptr是一个指针的指针
        // ptr[j] = new int[50]; // ptr[j]就是*(ptr+j) 是一个指针 让这个指针 指向一个int数组
        // 和上边那句是同样含义
    }
}
```

`a3d[i][j]` a3d是一个指向 指针的指针 的指针 `a3d[i]`是对指针的第一部分逆向引用 `a3d[i]`就是`*(a3d+i)` `a[i][j]`是对指针的第二部分逆向引用 `a[i][j]`就是`*(a3d[i]+j)=*(*(a3d+i)+j)`

回到二维数组

`delete[][] a2d;` 不存在这样的写法

```c++
for (int i = 0; i < 50; i++)
    delete[] a2d[i];
delete[] a2d; // 因为a2d其实只是一个int**
```

0行0列 `a2d[0][0]`是第一个元素
0行1列 `a2d[0][1]`的地址是`&a2d[0][0] + 1`
1行0列 `a2d[1][0]`的地址是`&a2d[0][0] + cols` 因为要跳过第一行的所有元素
所以访问`a2d[0][1]`是更快的 也就是**访问同一行的元素会更快**

我们没有一个连续的内存缓冲区 在一行中保存这50\*50=2500个整数 我们是创建了50个单独的缓冲区 会被分配到内存中完全随机的位置 没有办法保证一定离得很近 不能缓存命中 所以遍历这2500个整数 比只遍历一个2500个元素的一维数组慢得多 一维数组内存分配都在同一行 如果不用二维数组 有没有什么其它更好的办法存储这2500个整数 可以把它存储在一个一维数组中 

```c++
int* array = new int[50 * 50];
for (int y = 0; y < 50; y++)
    for (int x = 0; x < 50; x++)
        array[x + y * 50] = 0; // 这样就可以逐个初始化 array[0] ~ array[49] array[50] ~ array[99] ...
```

这样会更快 完全是一直在访问内存中的同一行

倾向于尽量避免使用二维数组 即使是图片像素 也可以存储成一维数组 没必要二维数组

# 排序 std::sort

需要给它提供一个开始迭代器和一个结束迭代器 迭代器内的所有东西都会基于我们提供的谓词进行排序

```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
    std::vector<int> values = { 3, 5, 1, 4, 2 };
    std::sort(values.begin(), values.end());

    for (int value : values)
        std::cout << value << std::endl;

    std::cin.get();
}

// 最后输出 1 2 3 4 5
```

如果不提供一个函数来进行排序 对于整数 它就会按升序排序

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <functional>

int main()
{
    std::vector<int> values = { 3, 5, 1, 4, 2 };
    std::sort(values.begin(), values.end(), std::greater<int>());

    for (int value : values)
        std::cout << value << std::endl;

    std::cin.get();
}

// 最后输出 5 4 3 2 1 变成从大到小排序
```

```c++
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
    std::vector<int> values = { 3, 5, 1, 4, 2 };
    std::sort(values.begin(), values.end(), [](int a, int b)
              {
                  if (a == 1)
                      return false;
                  if (b == 1)
                      return true;
                  // 最后达成的结果是 1会在所有其它数字的后面
                  return a < b;
              });

    for (int value : values)
        std::cout << value << std::endl;

    std::cin.get();
}

// 最后输出 2 3 4 5 1
```

比较函数要返回一个bool值 如果返回true a就会排在b之前 返回flase b就会排在a之前 

# 类型双关

比如我的代码中 有个整数 但我现在要把这段内存 同样的内存 当作double类型 重新解释

```c++
int a = 50;
double value = a; // 类型转换 int变成了double
std::cout << value << std::endl;
```

value和a的地址是不同的 现在是隐式转换 显式转换就是`double value = (double)a;`

```c++
int a = 50;
double value = *(double*)&a // 类型双关
```

找到a的地址 把它转换成double指针 然后再逆向引用 才能从指针回到原来的类型

a（4字节）在内存中是 32 00 00 00（50的十六进制） 将其强制当作double（8字节）读取时 会读取a地址开始的 8字节数据 32 00 00 00 ?? ?? ?? ??  包括a之后的4字节未知内容 ??是指不确定的未知数据 这会导致未定义行为 可能得到无意义浮点数

如果只想把这个int当作double来访问 不想创建一个全新的变量 就需要引用而不是拷贝

```c++
int a = 50;
double& value = *(double*)&a; // 引用
value = 0.0;
```

如果对value进行写入 就是把8字节的double数据写入4字节int的内存 会导致程序崩溃

```c++
struct Entity
{
	int x, y;
};

int main()
{
	Entity e = { 5, 8 };
	
	std::cin.get();
}
```

结构体内部不含任何的填充 查看内存&e就是可以看到 05 00 00 00 08 00 00 00 cc cc cc cc 如果是空的结构体 就至少是一个字节 为了可以寻址 但如果有变量 就不会有任何多余的数据 本例中就只存储了2个int

```c++
int* position = (int*)&e;
std::cout << position[0] << ", " << position[1] << std::endl;
// 会输出e.x e.y 即 5 8

int y = *(int*)((char*)&e + 4);
// 将e的地址转换成字节 然后增加4字节的地址 转换成int型指针 再逆向引用
std::cout << y << std::endl;
// 会输出e.y
```

写这种地址的思路是 **从取地址&e开始写 然后再转换成其它类型的指针** 

```c++
struct Entity
{
	int x, y;

	int* GetPositions()
	{
		return &x;
	}
}
```

这样用户就可以写

```c++
int* position = e.GetPosition();
position[0] = 2; // 这样就是修改了e.x
```

没有做任何特别的事情 类型双关 我要把我拥有的这段内存 当作不同类型的内存来对待 只是将该类型作为指针 然后将其转换为另一个指针 如有必要还可以对它进行解引用

# union

想给同一个变量取两个不同的名字时 很有用 通常union是匿名使用的 但是匿名union不能含有成员函数

```c++
int main()
{
	struct myUnion
	{
		union
		{
			float a;
			int b;
		};
	};

	myUnion u;
	u.a = 2.0f;
	std::cout << u.a << ", " << u.b << std::endl;

}

```

现在就会输出`2, 1073741824` 1073741824是浮点数形式的2的字节表示 就好像我们取了组成浮点数的内存 然后把它解释成一个整型

```c++
struct Vector2
{
	float x, y;
};

struct Vector4
{
	float x, y, z, w;

	Vector2 GetA()
	{
		// return Vector2(x, y);
		// 但是这样就会创建新的对象 虽然我们没写类初始化函数 所以这样写不合法 但暂时这样写 理解含义即可
		return *(Vector2*)&x; // 使用类型双关
	}
};

void PrintVector2(const Vector2& vector)
{
	std::cout << vector.x << ", " << vector.y << std::endl;
}
```

也许可以把vector4看成2个vector2 这样就可以从vector4中取出vector2

假如不用类型双关 而是用union 更易读

```c++
strct Vector4
{
	union
	{
		// 匿名struct
		// 现在这个结构体是union的一个成员 目前union只有一个成员
		struct
		{
			float x, y, z, w;
		};
	}
}
```

不能在union里直接写`float x, y, z, w;` 这样x y z w就会占用相同的空间 union里会有4个成员

```c++
Vector4 vector = { 1.0f, 2.0f, 3.0f, 4.0f };
vector.x = 2.0f;
```

现在我们这样使用都是正常的 因为我们没有给那个匿名结构体取名字 只要它是匿名的 它就只是一种数据结构

```c++
strct Vector4
{
	union
	{
		struct
		{
			float x, y, z, w;
		};
		struct
		{
			Vector2 a, b; // 两个vector
		};
	}
}
```

现在union里有两个成员 于是第二个成员和第一个成员占据相同的空间 那么现在就有多种访问Vector4内数据的方法 a和x, y的内存是一样的 b和z, w的内存是一样的

```c++
Vector4 vector = { 1.0f, 2.0f, 3.0f, 4.0f };
PrintVector2(vector.a);
vector.z = 500.0f;
PrintVector2(vector.b);
// 会输出
// 1, 2
// 500, 4
```

不是设置`b.x`为500 而是设置`vector.z`为500 但是它对应的就是`b.x` 因为占用了相同的内存

# 虚析构函数

```c++
class Base
{
public:
	Base() { std::cout << "Base Constructor\n"; }
	~Base() { std::cout << "Base Destructor\n"; }
 };

class Derived : public Base
{
public:
	Derived() { std::cout << "Derived Constructor\n"; }
	~Derived() { std::cout << "Derived Destructor\n"; }
 };

int main()
{
	Base* base = new Base();
	delete base;
	std::cout << "-------\n";
	Derived* derived = new Derived();
	delete derived;

	std::cin.get();
}
```

现在Derived类型同时也是Base类型 因为Derived是Base的子类

上面的代码会输出

```c++
Base Constructor
Base Destructor
-------
Base Constructor
Derived Constructor
Derived Destructor
Base Destructor
```

对于Derived类 首先调用了基类的构造函数 然后是Derived类的构造函数 所以会这样输出 现在就需要虚析构函数了 我们希望在析构子类的时候 只调用子类的析构函数

```c++
Base* poly = new Derived();
delete poly;
```

创建一个Derived实例 但是将它赋值给Base类类型 所以现在就把这种poly对象当作Base类指针来处理  但它实际上是一个指向Derived类型的指针

上面代码的执行结果就是

```c++
Base Constructor
Derived Constructor
Base Destructor
```

只调用了基类的析构函数 没有调用派生类的析构函数 这会导致内存泄露

虚函数在方法前标注virtual 使得可以在子类中重写这个方法 虚析构函数有些不太一样 不是覆写析构函数 而是加上一个析构函数 所以如果把基类的析构函数变成虚函数 它就会调用两个析构函数 会先调用派生类析构函数 然后在层级结构中向上 调用基类析构函数

但是我们为什么非得调用派生类的析构函数 只调用基类的析构函数不行吗？ 

```c++
class Derived : public Base
{
public:
	Derived() { m_Array = new int[5]; std::cout << "Derived Constructor\n"; }
	~Derived() { delete[] m_Array; std::cout << "Derived Destructor\n"; }
 };
```

现在我们在派生类中创建了一个数组 在析构时就需要删除该数组 如果只调用基类的析构函数 这个数组是无法被删除的 有内存泄露

现在将这个基类的析构函数标记为虚函数 意味着这个类有可能被扩展为子类 可能还有一个析构函数也需要被调用 如果有派生类的析构函数 就调用派生类的析构函数

```c++
class Base
{
public:
	Base() { std::cout << "Base Constructor\n"; }
	virtual ~Base() { std::cout << "Base Destructor\n"; }
 };
```

修改之后再执行

```c++
Base* poly = new Derived();
delete poly;
```

会输出

```c++
Base Cosntructor
Derived Constructor
Derived Destructor
Base Destructor
```

这就和

```c++
Derived* derived = new Derived();
delete derived;
```

输出结果一样 派生类的析构函数首先被调用 然后调用基类的析构函数 即使我们把它当作多态类型 当作基类类型来处理 它也能顺利调用子类的析构函数

但是我们为什么要创建多态类型？为什么要创建一个子类类型 并将其视为基类类型？

1. 通过基类统一接口操作不同派生类对象
    ```c++
    class Animal {
    public:
        virtual void speak() = 0;
        virtual ~Animal() {}
    };
    
    class Dog : public Animal {
        void speak() override { cout << “Woof!”; }
    };
    
    class Cat : public Animal {
        void speak() override { cout << “Meow!”; }
    };
    
    int main() {
        Animal* animals[] = {new Dog(), new Cat()};
        for (auto* a : animals) {
            a->speak();  // 通过统一接口调用不同实现
        }
    }
    ```
2. 运行时多态

**只要会写子类 就声明基类的析构函数为虚函数**

# 类型转换

隐式转换

```c++
int a = 5;
double value = a;
```

显式转换

```c++
double value = 5.25;
int a = (int)value;
```

```c++
double value = 5.25;
int a = (int)value + 5.3;
// a是10.3 而不是10.55
```

```c++
double value = 5.25;
int a = (int)(value + 5.3);
// a是10 截断了
```

上面都是C语言风格的类型转换

```c++
double value = 5.25;
double s = static_cast<int>(value) + 5.3;
```

C++的转换有很多种
static_cast 静态类型转换
reinterpret_cast 把这段内存重新解释成别的东西
dynamic_cast 暂时不介绍
const_cast 移除或者添加变量的const限定
它们并没有能力比C风格类型转换做更多的事情 只是语法糖 好处是可以通过搜索static_cast之类的 来找到做了类型转换的地方 C语言风格的就难以搜索

```c++
double s = static_cast<AnotherClass>(value);
// 总之AnotherClass是一个类 这样强制转换不行 在value那里标红 是构造函数引起的
```

value现在是int类型 如果AnotherClass有一个接受value类型的构造函数（非explicit） 那么static_cast可以调用该构造函数创建一个临时对象

如果有了构造函数 这种强制转换就是有效的 因为可以创建一个临时对象 再赋值给转换之后的对象

**static_cast在用于类类型时 会尝试调用相应的构造函数（或者类型转换函数）来创建目标类型的对象** 具体来说 如果目标类型有一个构造函数接受源类型（或者可以隐式转换到源类型）的参数 那么就会调用这个构造函数创建一个临时对象 或者 如果源类型定义了一个到目标类型的类型转换运算符 那么也会被调用

上面的这种写法 仿佛和使用 `AnotherClass obj = AnotherClass(value);` 直接调用构造函数 并没有什么区别 但是直接写static_cast更能表达 这是类型转换 的意图 两种写法在性能和行为上 几乎没有差别

上面是类类型的强制转换 而对于基本类型的转换 编译器知道它们之间的转换规则 **static_cast允许基本类型之间的转换 只要它们是数值类型 或者是指针和布尔值之间的转换等**

```c++
double s = static_cast<AnotherClass*>(value);
// 现在static_cast标红 无效的类型转换
```

value被当作指针值来使用 然后尝试转换为`AnotherClass*` 但value本身是一个int 不是指针 所以转换无效
即使value是一个指针 如果不是指向AnotherClass或其派生类 这种转换也是不安全的（除非在类继承关系中）

此外 如果写 `double s = static_cast<AnotherClass*>(value) + 5.3;` 将指针与5.3相加在语义上也不正确 指针加法是以指向类型的大小为单位的 比如p是int型指针 p+1的地址实际上是增加了sizeof(int) 个字节

```c++
double s = static_cast<AnotherClass*>(&value);
// 现在我们取内存地址 得到int指针 试图类型双关 仍然是在static_cast标红
```

对于类型双关 我们需要使用reinterpret_cast

```c++
double s = reinterpret_cast<AnotherClass*>(&value);
```

现在 我们就已经将value指针处的数据重新解释为AnotherClass实例的数据

C++风格的类型转换可以帮我们检查 它知道我们不能做某些转换 但如果是C风格的类型转换 就没办法知道了

```c++
Derived* derived = new Derived();

Base* base = derived;
// 将Derived实例转换成Base类型
// 向上转换 从派生类到基类 多态
```

问题变成 现在我有一个Base指针 它是一个Derived类的实例呢 还是AnotherClass类的实例 这两个类都是Base的子类

<span id="mypoint_16"></span>我们现在已经知道 base实际上是Derived类的一个实例 但我们假装不知道

```c++
AnotherClass* ac = dynamic_cast<AnotherClass*>(base);
```

如果我们使用的是static_cast 这样做就没问题 这和C语言的类型转换是一样的
但实际上我们知道 ac不是Another Class的实例 而是Derived的实例 我们只是做了一个类型双关 但是dynamic_cast就会查看 是否可以这样转换

dynamic_cast用于在继承层次中进行安全的向下转换
如果base实际指向的对象是AnotherClass类型（或它的派生类）或者是从AnotherClass派生的类型 那么转换成功
否则 如果转换是指针类型 则返回nullptr（对于引用则会抛出`std::bad_cast`异常）

**向上转换**
从Derived类型或者AnotherClass类型转换到Base类型总是安全的 直接转换就可以了 因为这就是多态的机制
也可以写static_cast 会看起来更清晰 也不会带来性能损失

**向下转换**
如果要把Base类型转换为Derived或者AnotherClass类型
如果base确实指向一个Derived对象 那么转换是安全的 但如果base指向的是其他类型（比如另一个派生类）或者就是Base类的对象 那么转换后访问派生类特有的成员将导致未定义行为

这时就需要dynamic_cast 它会在运行时检查转换是否安全
如果转换是安全的（即基类指针确实指向目标派生类的对象） 则返回转换后的指针
否则 返回nullptr（对于指针类型）或抛出异常（对于引用类型）
所以我们在使用dynamic_cast转换完之后 必须要自己手动检查 有没有返回nullptr或者抛出std::bad_cast异常

依赖于运行时类型信息RTTI 暂时我们不过多讨论dynamic_cast了

const_cast是用来给变量添加或者移除const的 尽量不要使用
当函数接受非const指针/引用 但你只有const对象时（且确定该对象原本不是常量） 可用const_cast安全转换 调用遗留的非const API（无法修改源码）和处理设计不佳的第三方库接口时使用

reinterpret_cast 是没有转换什么东西 只是想把这个现有的内存解释成别的东西 和类型双关是一个意思

尽量使用C++的cast 对大家都好 尽量避免C风格的类型转换

# 条件与操作断点

我们希望在程序运行时 去修改代码再调试

打断点 对断点右键 - 条件 会看到条件前面被勾选了 条件表达式可以是任何的布尔语句 也可以勾选操作 输出一些东西 条件和操作同时勾选就可以同时使用 不需要停止应用程序 也没有重新编译代码

# C++安全

尽量使用智能指针 就能自动释放内存 我们仍然需要学习原始指针 需要知道内存是如何工作的 但如果代码很多就会变得难以管理 停止关于原始指针和智能指针的争论 都可以用 自由地编写代码 智能指针只是原始指针上的包装 本质上只是能自动删除和释放内存 不应该害怕原始指针

# 预编译头文件

预编译头文件是抓取一堆头文件 并将它们转换成编译器可以使用的格式 而不必一遍一遍地读取这些头文件

实际上我们每次include头文件时 都是读取整个头文件 然后编译它 而且你调用的这个头文件可能还包含其它头文件 都要被复制过来 于是在你想要编译main文件之前 所有的代码每一次都要被解析和编译 就算是不同的cpp文件有相同的头文件 由于这个头文件是单独包含在每个文件中的 每一个翻译单元都是单独编译 然后再进行链接 每次你对cpp文件进行修改 整个文件都要重新编译 头文件每次都要开始重新解析并重新编译
这时需要使用预编译头文件 作用是接收一堆你告诉它要接收的头文件 它只编译一次 以二进制格式存储 这对编译器来说比单纯的文本处理要快得多 每次你include里预编译的头文件 它就已经有了你需要的一切

我们自然地想到 也可以把项目里很多**不作修改的东西** 比如自己写的日志Log类头文件 很多cpp文件都会使用它 但你几乎不会修改它 都放到预编译头文件中 来节约编译时间 到时候只要包含一个预编译头文件就行了 它内部已经有Log了

预编译头文件pch真正的用处是 外部依赖 比如STL 第三方API 但是如果你把它全都放在pch里 使用的时候 只知道你include了pch 但是不知道具体是用了哪个第三方库 也不知道是需要哪个文件 而且**有些库可能只有个别几个cpp文件才需要使用 就不能放在pch里**让所有cpp文件都添加上它 应该放进pch的是STL这种高频使用的

```c++
#include "pch.h"

int main()
{
    std::cout << "Hello World" << std::endl;
}
```

这里include的是pch.h Visual Studio默认是写成stdafx.h 如果用c++模板创建项目的话 当然我们平时都是用空项目创建的 我们只是在pch.h中包含了一堆其它的头文件 可能像这样

```c++
// pch.h

#pragma once

#include <iostream>
#include <algorithm>
#include <functional>
#include <memory>
#include <thread>
#include <utility>

#include <string>
#include <stack>
#include <deque>
#include <array>
#include <vector>
#include <set>
#include <map>
#include <unordered_set>
#include <unordered_map>

#include <windows.h>
```

**一旦你有了头文件 就需要再做一个包含头文件的cpp文件** 这是Visual Studio的做法 所以我们还需要再新建一个pch.cpp

```c++
// pch.cpp

#include "pch.h"
```

右键pch.cpp - 属性 - C/C++ - 预编译头 在预编译头文件处 编辑写入 pch.h 然后 预编译头 改成 创建 预编译头输出文件的那个.pch 就是预编译头文件在编译后的二进制格式
右键整个项目 - 属性 - C/C++ - 预编译头 在预编译头文件处 编辑写入 pch.h 然后 预编译头 改成 使用 这样就会适用到所有的文件 现在你打开右键main.cpp - 属性 - C/C++ - 预编译头 就会发现已经配置好了

我们想查看main.i 先要右键main.cpp - 属性 - C/C++ - 预处理器 - 预处理到文件 选择是

然后开始生成项目 编译器肯定会报链接错误 说没找到main.obj 不用理会 来到`Project_test\bin\intermediates\x64\Debug`文件夹 找到main.i 里面有40多万行 前面都是头文件 这就是每次都要重新编译的内容 最后几行才是我们的main函数

别忘了把预处理到文件关掉

现在我们要对比使用预编译头前后的差异

右键项目 - 属性 - C/C++ - 预编译头 换成不使用预编译头
上方菜单栏点击 工具 - 选项 - 项目和解决方案 - VC++项目设置 生成计时改为 是

清理之后 生成项目 05.596秒 修改main.cpp 加一行`std::cout << "Hello World" << std::endl;` 加这一行没有什么特别的意义 只是测速 不要清理 再生成一次 05.322秒
换成使用预编译头 清理 生成项目 03.489秒 修改main.cpp 加一行 不要清理 生成 01.973秒

提速明显 可以发现即使是首次编译 使用预编译头也比不适用更快 这是因为 首次生成时 编译器会先把pch.h里包含的大量头文件一次性编译成 .pch 文件 这样后续编译 main.cpp 时 遇到 `#include "pch.h"` 就直接加载 .pch 不用再重复分析和编译这些头文件 项目越大 头文件越多 效果越明显

不存在什么需不需要用预编译头文件的问题 每一个项目都需要用 问题就是你应该往预编译头文件里放什么

# dynamic_cast

[dynamic_cast](#mypoint_16)更像是一个函数 它不是编译时进行的类型转换 而是在运行时计算 所以它会有运行成本

dynamic_cast是专门用于沿继承层次结构进行的强制类型转换 比如想从派生类型转换为基类类型 或者从基类类型转换为派生类型 假如我们有一个Entity实体类 它实际上是一个Enemy敌人 但我们尝试使用dynamic_cast将其转换为一个Player玩家 这个转换就会失败 dynamic_cast会返回一个NULL指针 也就是0 所以我们可以尝试在Entity对象上进行dynamic_cast 将其转换为Player对象 检查它是否返回NULL 如果返回为NULL 那就不是Player

```c++
class Entity
{
};

class Player : public Entity
{
};

class Enemy : public Entity
{
};
```

```c++
Player* player = new Player();
```

这里用的是原始指针 智能指针暂时不讨论 现在这个player已经有两种类型了 Player和Entity 我们可以直接写 `Entity* player = new Player();` 隐式转换

```c++
Entity* e = player; // 隐式转换
```

从子类转换到基类 没有任何特殊的 直接写就可以 但如何从基类转换到子类 直接写 `Player* p = e;` 会报错 因为编译器不知道e指向的是什么类型 也有可能是Enemy类型 我们必须明示编译器 这个新的Player对象接收的就是一个Player类型

```c++
Entity* e1 = new Enemy();
```

于是`Player* p = e1;` 就报错 因为e1明显指向的是Enemy类型 而我们必须向编译器保证 这是一个Player类型 于是强制转换 `Player* p = (Player*)e1;` 但这样不安全

`Player* p = dynamic_cast<Player*>(e);` e是一个从Player类转过来的Entity类型 编译器报错说 e必须是一个多态类型 因为dynamic_cast只用于多态类型

我们需要一个虚函数表

```c++
class Entity
{
public:
    virtual void PrintName() {}
};
```

随便写什么虚函数 总之是要有一个虚函数表 这样就有了需要override的东西 意思就是它是多态类型 现在就可以使用类型转换 当然了真正的Entity类是必然有虚函数的

现在`Player* p = dynamic_cast<Player*>(e);` 就可以成功转换 e实际上指向一个Player类型的对象 那么dynamic_cast会返回一个指向该Player对象的指针 也就是Player\* 并且该指针的值与e原本指向的地址相同

疑问：e本来就是多态的 本来就既是Entity也是Player 那实际上就是把它作为Player的样子赋值给Player\* p？

e的类型在编译时就是Entity\* 无论你怎么dynamic_cast e的类型都不会改变 也不会改变它指向的对象

`dynamic_cast<Player*>(e)` 的作用是 尝试把e作为 Player\* 类型来使用 如果e实际上指向的是Player对象 则转换成功 返回一个指向同一对象的 Player\* 指针 否则返回nullptr

e只是看待这个对象的方式不同 本质上对象没变 `Player* p = dynamic_cast<Player*>(e);` 只是把e作为Player\*的视角赋值给p 如有可能的话

`Player* p1 = dynamic_cast<Player*>(e1);` e1是一个从Enemy类型转换过来的Entity类型 所以转换会失败 dynamic_cast返回nullptr

但编译器是怎么知道的呢 怎么知道能不能支持转换 知道这个Entity实际上是Player而不是Enemy 因为它存储运行时类型信息runtime type information RTTI 存储着所有类型的运行时类型信息 是会增加开销 但是可以让你做动态类型转换之外的事 而且dynamic_cast由于需要检查类型信息是否匹配 也有开销

可以在代码中关闭运行时类型信息 右键项目 - 属性 - C/C++ - 语言 - 启用运行时类型信息 选择否 现在dynamic_cast就会报错

```c++
Player* p1 = dynamic_cast<Player*>(e1);
if (dynamic_cast<Player*>(e1))
// e1是否是Player的实例
// 如果是 dynamic_cast返回值非空 可以进入条件语句
// 如果不是 dynamic_cast返回值为nullptr 无法进入条件语句
// 当然这里完全可以写成 if (p1)
{
    // do something
}
```

# 基准测试 Benchmark Test

测试C++代码的性能

```c++
#include <iostream>
#include <memory>

int main()
{
    int value = 0;
    for (int i = 0; i < 1000000; i++)
        value += 2;

    std::cout << value << std::endl;

    __debugbreak(); // visual studio专门用于windows的函数
    // 在这里中断编译 这样就不用自己设置断点了
}
```

会得到2000000 现在分析代码到底有多快

```c++
#include <iostream>
#include <memory>

#include <chrono>

class Timer
{
public:
    Timer()
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    ~Timer()
    {
        Stop();
    }

    void Stop() 
    {
		auto endTimepoint = std::chrono::high_resolution_clock::now();

		auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        auto duration = end - start;
        double ms = duration * 0.001;

        std::cout << duration << "μs (" << ms << "ms)\n";
    }
private:
	std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
};

int main()
{
    int value = 0;
    {
        Timer timer;
        for (int i = 0; i < 1000000; i++)
            value += 2;
    }

    std::cout << value << std::endl;

    __debugbreak();
}
```

会输出1704μs (1.704ms)

debug模式下反汇编查看 确实是做了很多次value+2的操作 真的做了加法 但是release模式下 就都被优化掉了 这样我们其实什么都没有计时到 所以无论你在测试什么 都需要确保你确实做了这些事情 不能测量什么都没发生的事情 因为编译器很有可能就优化掉了

[shared_ptr](#mypoint_17)

```c++
#include <iostream>
#include <memory>

#include <chrono>
#include <array>

class Timer
{
public:
    Timer()
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    ~Timer()
    {
        Stop();
    }

    void Stop() 
    {
		auto endTimepoint = std::chrono::high_resolution_clock::now();

		auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        auto duration = end - start;
        double ms = duration * 0.001;

        std::cout << duration << "μs (" << ms << "ms)\n";
    }
private:
	std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
};

int main()
{
    struct Vector2
    {
        float x, y;
	};

    std::cout << "Make Shared\n";
    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        Timer timer;
        for (int i = 0; i < sharedPtrs.size(); i++)
        {
            sharedPtrs[i] = std::make_shared<Vector2>();
        }
    }

    std::cout << "New Shared\n";
    {
        std::array<std::shared_ptr<Vector2>, 1000> sharedPtrs;
        Timer timer;
        for (int i = 0; i < sharedPtrs.size(); i++)
        {
            sharedPtrs[i] = std::shared_ptr<Vector2>(new Vector2());
        }
    }

    std::cout << "Make Unique\n";
    {
        std::array<std::unique_ptr<Vector2>, 1000> uniquePtrs;
        Timer timer;
        for (int i = 0; i < uniquePtrs.size(); i++)
        {
            uniquePtrs[i] = std::make_unique<Vector2>();
        }
    }

    __debugbreak();
}
```

debug模式下 输出结果

```c++
Make Shared
506μs (0.506ms)
New Shared
1050μs (1.05ms)
Make Unique
234μs (0.234ms)
```

release模式下 输出结果

```c++
Make Shared
99μs (0.099ms)
New Shared
140μs (0.14ms)
Make Unique
108μs (0.108ms)
```

make_shared明显比new shared更快

# 结构化绑定

能让我们更好地处理[多返回值 ](#多返回值) 可以用tuple pair 也可以用结构体

```c++
#include <iostream>
#include <string>
#include <tuple>

std::tuple<std::string, int> CreatePerson()
// 返回姓名和年龄的tuple 用pair也行 但是tuple可以用更多参数
{
	return { "Miku", 17 };
}

int main()
{
	std::tuple<std::string, int> person = CreatePerson();
	// 可以直接用auto来取代std::tuple<std::string, int>

	std::string& name = std::get<0>(person); // 过于magic
	int age = std::get<1>(person);
}
```

实际上**没有真正的person变量 不是结构体 不是一个类型 只是一个容器 存放着我们想要的数据 一个string和一个int**

```c++
int main()
{
    std::string name;
    int age;
    std::tie(name, age) = person;
}
```

这种是看起来更好 但仍然是三行代码 感觉不如结构体

```c++
struct Person
{
	std::string Name;
    int Age;
}
```

现在就可以用person.name person.age来获取数据

结构化绑定 C++17引入 右键项目 - 属性 - C/C++ - 语言 - C++语言标准 换成C++17

```c++
#include <tuple>

std::tuple<std::string, int> CreatePerson()
{
	return { "Miku", 17 };
}

int main()
{
	auto[name, age] = CreatePerson();
	std::cout << "Name: " << name << ", Age: " << age << std::endl;
}
```

回到当时我们那个[Shader的例子](#mypoint_18)

```c++
struct ShaderProgramSource
{
    std::string VertexSource;
    std::string FragmentSource;
};

static ShaderProgramSource ParseShader(const std::string& filepath)
{
    // do something
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    return { vs, fs };
}
```

```c++
std::tuple<std::string, std::string> ParseShader(const std::string& filepath)
{
    // do something
    std::string vs = ss[0].str();
    std::string fs = ss[0].str();
    
    return { vs, fs };
}
```

使用的时候 不再是

```c++
ShaderProgramSource source = ParseShader(filepath);
m_RendererID = CreateShader(source.VertexSource, source.FragmentSource);
```

而是

```c++
auto[vertexSource, fragmentSource] = ParseShader(filepath);
m_RendererID = CreateShader(vertexSource, fragmentSource);
```

因为实际上这个为了制作返回值的结构体 几乎不会被再次使用 会产生一个多余的类型

# std::optional

C++17新特性 存储可能存在也可能不存在的数据

```c++
#include <iostream>
#include <fstream>

std::string ReadFileAsString(const std::string& filePath)
{
	std::ifstream stream(filePath);
	// 输入文件流
	// 如果文件打开成功 又或者无法打开 要处理它
	if (stream)
	{
		std::string result; // 用于存储从文件中读取的内容
		// read_file
		stream.close();
		return result;
	}
	
	// 如果不成功
	return std::string();
	// 返回空字符串对象 利用std::string的默认构造函数 等价于std::string("")
}

int main()
{
	std::string data = ReadFileAsString("data.txt");
	if (data != "")
	// 但是假如文件就在那里 它是空的 但它是有效的 我们需要一种方式确定它是否有效
	{
		//
	}
```

或者用引用输出一个bool值

```c++
#include <iostream>
#include <fstream>

std::string ReadFileAsString(const std::string& filePath, bool& outSuccess)
{
	std::ifstream stream(filePath);
	if (stream)
	{
		std::string result;
		// read_file
		stream.close();
		outSuccess = true; // 表示读取成功
		return result;
	}
	
	outSuccess = false; // 表示读取失败
	return std::string();
}

int main()
{
	bool fileOpenSuccessfully;
	std::string data = ReadFileAsString("data.txt");
	if (fileOpenSuccessfully)
	{
		//
	}
```

还不够好

`std::optional` 数据是否存在是可选的

```c++
#include <iostream>
#include <fstream>
#include <optional>

std::optional<std::string> ReadFileAsString(const std::string& filePath)
{
	std::ifstream stream(filePath);
	if (stream)
	{
		std::string result;
		// read_file
		stream.close();
		return result;
	}
	
	return {};
}

int main()
{
	std::optional<std::string> data = ReadFileAsString("data.txt");
	if (data.has_value())
		// 这里可以写if(data)
		// 实际上调用了data的operator bool() 会返回true或者false
		// 这和 if (data.has_value()) 的效果完全一样
	{
		std::cout << "File read successfully!\n";
	}
	else
	{
		std::cout << "File could not be opened!\n";
	}
}
```

使用data时就用`std::string& string = *data;` 或者`data.value();`

`std::optional<T>` 模板类 用来表示 可能有值也可能没有值 的情况
当data是`std::optional<std::string>`时 data不是字符串本身 而是一个容器 里面可能装着一个`std::string` 也可能什么都没有
**`*data`的意思是 取出 optional 里装着的那个值 也就是std::string对象本身 不是指针的逆向引用 是std::optional类型的 解包 操作**
**data是optional类型 不是string类型 不能直接当作字符串用**

data.txt必须在项目目录里 也就是.vcxproj所在的目录 如果data.txt放在了src文件夹里 就需要写相对路径 `src/data.txt`

`std::string value = data.value_or("No present")`
如果数据确实存在于std::optional中 它将返回给我们那个字符串 如果不存在 它会返回我们传入的任何值

```c++
std::optional<int> count;
int c = count.value_or(100);
```

如果文件中存在 就提取这个计数 如果不存在 就使用我们设置的100

# std::variant

C++17新特性 单一变量存放多种类型数据 不用担心处理的确切数据类型

```c++
#include <variant>

int main()
{
	std::variant<std::string, int> data;
	data = "Miku";
    std::cout << std::get<std::string>(data) << "\n";
	data = 39;
    std::cout << std::get<int>(data) << "\n";
}
```

既可以赋值成字符串 也可以赋值成整数

```c++
data = 39;
std::cout << std::get<std::string>(data) << "\n";
```

如果我们混淆了类型 本例中就是把int当成了string std::get会为我们抛出异常 throw bad variant access

`data.index()` 会返回类型的索引 告诉我们数据当前在哪个索引之中 本例中std::string的索引是0 int是1 你当然可以写如果它是1 就`std::get<std::string>(data)` 这样的条件语句

更好的方式是 `std::get_if<std::string>(&data);` 需要传std::variant的内存地址 会返回一个指针 我们可以检查这个指针是否为空 如果是那个类型 就返回指向那个字符串的指针 如果不是那个类型 就返回空指针 `*(std::get_if<std::string>(&data))`就是这个字符串的值

```c++
if (auto value = std::get_if<std::string>(&data))
// 如果是std::string 就会进入条件语句 做一些对字符串的操作
{
    std::string& v = *value; // 因为我们知道value是指针 所以逆向引用
}
else
{
    // 处理另一种类型
}
```

std::variant和[union](#union)不是一样的
union的大小是它里面最大类型的大小 不同类型数据占有的是同一块内存
std::variant只是将所有可能的类型数据存储为单独的变量 作为单独的成员 但你在同一时间内只能访问一个单独的数据 std::variant类型变量的大小并不是简单地将所有类型大小相加 是它里面最大类型的大小 再加上一个用于存储当前类型的索引discriminator以及对齐填充

union是更有效率的 但是std::variant更加类型安全 不会造成未定义行为 可以使用它 除非在做底层优化或者想使用尽可能少的内存

```c++
enum class ErrorCode
{
	None = 0, NotFound = 1, NoAccess = 2
};

// 读取成功就返回字符串 失败就返回错误码 比返回bool值更详细一些
std::variant<std::string, ErrorCode> ReadFileAsString(const std::string& filePath)
{
	return {};

}
```

# std::any

C++17新特性 单个变量中存储任意类型的数据

也许可以用void指针做 暂时我们先不讨论

```c++
#include <any>

std::any data;
data = 39;
data = "Miku";
data = std::string("Miku"); // 这里就是将const char*隐式转换为std::string
```

std::variant要求列出所有类型 反而使得类型安全

实际上`data = "Miku";` 这时候data是一个const char\* 因为"Miku"是一个字符串字面量 其类型是const char[5] (包括结尾的空字符 \0) 而数组在赋值时会退化为指针 所以std::any实际存储的是const char*

如果你使用的是只列举了std::string而没有列举const char\*的std::variant 在做`data = "Miku";`赋值的时候 会隐式转换成std::string 而不是const char\*
但如果是std::any 就必须要`std::any_cast<const char*>(data)` 才能把这个值取出来 并不会隐式转换成std::string

```c++
std::any data;
data = "Miku";

std::string value = std::any_cast<const char*>(data);
std::cout << value << std::endl;
```

发现输出的是Miku 而不是那个const char\* 不是一个指向这个字符串首地址的指针 因为在赋值给value的时候 std::string有一个能接收const char\*的构造函数 因此发生了隐式构造 最后输出的就是一个std::string

```c++
std::any data;
data = "Miku";

std::cout << std::any_cast<const char*>(data) << std::endl;
```

输出了Miku 仍然没有输出一个const char\*指针 这是因为std::cout遇到const char\*或者char\* 会自动解引用这个指针 将它视为C风格的字符串 也就是以\0结束的字符数组 它会输出这个字符串的内容 直到遇到\0为止

```c++
const char* ptr = std::any_cast<const char*>(data);
std::cout << ptr;
```

所以即使我们这样显式获取了指针 它还是会输出Miku字符串 而不是指针

```c++
std::cout << static_cast<const void*>(std::any_cast<const char*>(data)) << std::endl;
```

必须强制类型转换 将它转换成const void\*或者void\*才可以输出指针

对于小的数据类型 std::any的存储和std::variant一样 超过32字节 就会调用new和动态内存分配 std::variant就不用动态分配内存 性能会更好 实在没必要用std::any 几乎没有必要在单个变量中存储任意类型数据

# 多线程 std::async

```c++
void EditorLayer::LoadMeshed()
{
	// do something

	for (const auto& file : meshFilepaths)
		m_Meshs.push_back(Mesh::Load(file));

}
```

这是一个游戏场景 总之是for循环逐个地从文件中加载网格 在每次迭代中加载网格 然后再继续下一次迭代之前等待那个网格被加载完 做并行for循环在C++中非常困难 

```c++
// EditorLayer.cpp 截取

#include <iostream>
#include <future>

// do something

static std::mutex s_MeshesMutex;
// Meshes的意思是mesh的vector数组

static void LoadMesh(std::vector<Ref<Mesh>>* meshes, std::string filepath)
// meshes是复制指针 复制mesh的内存地址 不能使用引用 要复制
// 这个meshes是EditorLayer类的一个成员变量 其生存期与EditorLayer对象一样长 而不仅仅是在LoadMeshed函数执行期间
// filepath是复制 因为meshFilepaths是EditorLayer函数的局部变量 在作用域结束之后就会被销毁
{
	auto mesh = Mesh::Load(filepath);

	std::lock_guard<std::mutex> lock(s_MeshesMutex);
	// 这个锁会在lock_guard对象被创建时自动锁定mutex互斥锁
	// 退出这个函数就会解锁 因为lock_guard对象的析构函数会自动调用unlock
	// 看不懂也没关系 暂时不过多讨论mutex
	meshes->push_back(mesh);
	// 如何并发地把mesh push_back到meshes？
	// 我们必须锁定这个mesh vector 当它被修改时 我们就锁定它 push_back之后 我们就解锁它 所以上一句代码里设置了锁
	// 在我们完成当前线程的push_back之前 如果另一个mesh正在并发加载 试图同时push_back
	// 它就会等待 直到我们完成 直到我们解锁那个mesh vector
	// 我们解锁那个mesh vector之后它就可以继续push_back了

}

void EditorLayer::LoadMeshed()
{
	std::ifstream stream("src/Models.txt"); // 从这个文件中读取需要加载的模型路径
	std::string line;
	std::vector<std::string> meshFilepaths;

	// do something

#define ASYNC 1
#if ASYNC
	for (const auto& file : meshFilepaths) {
		m_Futures.push_back(std::async(std::launch::async, LoadMesh, &m_Meshes, file));
		// 要传入m_Meshes的内存地址
        // m_Futures是EditorLayer类的一个成员变量 std::vector<std::future<void>> m_Futures;
	} 
#else
    // 这是不做异步的方案
	for (const auto& file : meshFilepaths)
		m_Meshs.push_back(Mesh::Load(file));
}
```

`std::async(std::launch::async, LoadMesh, &m_Meshes, file)`
本例中我们希望在一个单独的线程上异步地完成 所以用`std::launch::async` 如果设置成`std::launch::deferred`可能不会在一个单独的线程上完成 而是C++根据当前工作负载来选择 是实际异步运行的函数 LoadMesh是要并行的函数

std::async执行时 会立即启动一个异步任务 并返回一个std::future 这个future用来获取异步任务的结果或等待任务完成 你需要保留这个值 如果没有保存future 那么在std::async执行结束时 这个临时的future对象就会被销毁 而future的析构函数要等待std::async创建的那个异步任务完成 所以根本没有任何并发效果 因此必须立即保存
`m_Futures.push_back(std::async(std::launch::async, LoadMesh, &m_Meshes, file));`

可以在调用栈里看到多个线程 菜单栏的调试 - 窗口 - 并行堆栈 可以看到图表

# std::string_view

C++17新特性 让std::string运行得更快

[在堆上进行内存分配](#new)不一定是坏事 但是要尽量避免

```c++
#include <iostream>
#include <string>

static uint32_t s_AllocCount = 0; // 表示分配的次数

void* operator new(size_t size)
{
	s_AllocCount++;
	std::cout << "Allocating " << size << "bytes\n";

	return malloc(size);
}

void PrintName(const std::string& name)
{
	std::cout << "Name: " << name << std::endl;
}

int main()
{
	std::string name = "Hatsune Miku";
	PrintName(name);

	std::cout << s_AllocCount << " allocations totally"  <<std::endl;

	std::cin.get();
}
```

重载new 可以查看程序中隐式地new了的地方

`uint32_t` 无符号32位整数类型 u表示unsigned int表示整数 32表示32位 _t表示type 类型

`size_t` 无符号整数类型 大小依赖于平台 32位系统上就是32位 64位系统上就是64位 可以根据平台自动调整大小 这样它的表示范围就可以表示该平台上能分配的最大内存块大小 而且语义明确 不是随便的无符号整数 差不多就是把无符号整数封装成了一个新的数据类型

上面代码的运行结果

```c++
Allocating 16bytes
Name: Hatsune Miku
1 allocations totally
```

在初始化name的时候 分配了一次内存 分配了16字节

如果把

```c++
std::string name = "Hatsune Miku";
PrintName(name);
```

改成

```c++
PrintName("Hatsune Miku");
```

也还是一样分配内存 没有什么区别 尽管`"Hatsune Miku"`是const char[12] 但需要构造一个std::string 构造需要分配内存

```c++
std::string name = "Hatsune Miku";

std::string firstName = name.substr(0, 7);
// 前7个字符组成的字符串 是first name
std::string lastName = name.substr(8, 12);
// 跳过了中间的空格

PrintName(firstName);
PrintName(lastName);
```

这样就是分配3次内存 每次都分配16字节 随随便便做了一些操作 就分配了3次 这样的事情每时每刻都在我们的程序中发生 

为了得到firstName的那几个字符 我们真的需要创建一个子字符串吗 我们现在的操作是将我们所需的数据复制到了一个新的firstName字符串变量中 如果写`PrintName(name.substr(0,7));` 这也是会分配一次内存

**std::string_view** 是一个指向现有内存的指针 就是一个const char\* 指向其它人拥有的现有字符串 再加上一个大小size

比如Hatsune Miku 可以有一个指向第一个字符的指针 大小是7 这是firstName 另一个指针指向这个字符串的开头再加上8个字节 也就是lastName的开头 大小是4

实际上你是在创建一个窗口 一个进入现有内存的小视图 而不是用substr()分配一个新的字符串 我们只是需要到达一个已有内存的字符字符串 不是在创建自己的字符串 而是在观察一个已有的字符串

```c++
std::string name = "Hatsune Miku";

std::string_view firstName(name.c_str(), 7);
// 通过构造函数来指定子字符串 
// name.c_str()是字符串name的const char*类型
// 指定长度是7
std::string_view lastName(name.c_str() + 8, 4);

PrintName(firstName);
PrintName(lastName);
```

现在就是1次分配 注意要把PrintName接收的类型从`const std::string&` 改成`std::string_view`

还可以优化

`"Hatsune Miku"`是一个静态字符串 没有理由一定要变成std::string 完全可以直接用const char\* 这样name本身就是一个指针 也就不再需要c_str **如果不是静态字符串 还是用std::string更好**

```c++
const char* name = "Hatsune Miku";

std::string_view firstName(name, 7);
std::string_view lastName(name + 8, 4);

PrintName(firstName);
PrintName(lastName);
```

现在就是完美的0次分配

我们已经将`PrintName()` 改为接收`std::string_view` 所以现在做`PrintName("Miku");` 也是不会导致内存分配
但如果是接收`const std::string&` 即使这是一个常量引用 由于`"Miku"`是一个字符串字面量 要先将它隐式转换成std::string 才能传入PrintName 这个初始化成为std::string的过程 就会发生一次内存分配

# 可视化基准测试

```c++
#include <iostream>
#include <string>
#include <chrono>

#include <cmath>

class Timer
{
public:
    Timer(const char* name)
		: m_Name(name), m_Stopped(false)
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    void Stop()
    {
        auto endTimepoint = std::chrono::high_resolution_clock::now();

        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        auto duration = end - start;
        double ms = duration * 0.001;

        std::cout << m_Name << ": " << duration << "μs (" << ms << "ms)\n";

        m_Stopped = true;
    }

    ~Timer()
    {
        if(!m_Stopped)
            Stop();
    }

private:
	const char* m_Name; // 计时器的名字
	bool m_Stopped;
    std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
};


// 需要测试性能的函数
void Function1()
{
	Timer timer("Function1");

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World!" << std::endl;
}

void Function2()
{
    Timer timer("Function2");

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World! #" << sqrt(i) << std::endl;
}

int main()
{
	Function1();
	Function2();

	std::cin.get();
}
```

复用了之前[基准测试](#基准测试)的Timer类

成功计算了都用多少时间 但是必须在控制台查看 很麻烦 

打开chrome浏览器 进入`chrome://tracing/`网页

```c++
#include <iostream>
#include <string>
#include <chrono>
#include <algorithm>
#include <fstream>

#include <thread>

#include <cmath>

struct ProfileResult
{
    std::string Name;
    long long Start, End;
};

struct InstrumentationSession
{
    std::string Name;
};

class Instrumentor
{
private:
    InstrumentationSession* m_CurrentSession;
    std::ofstream m_OutputStream;
    int m_ProfileCount;
public:
    Instrumentor()
        : m_CurrentSession(nullptr), m_ProfileCount(0)
    {
    }

    void BeginSession(const std::string& name, const std::string& filepath = "profile.json")
    {
        m_OutputStream.open(filepath);
        WriteHeader();
        m_CurrentSession = new InstrumentationSession{ name };
    }

    void EndSession()
    {
		WirteFooter();
        m_OutputStream.close();
        delete m_CurrentSession;
        m_CurrentSession = nullptr;
		m_ProfileCount = 0;
    }

    // 核心函数 以ProfileResult结构体为参数 包含name start end
    void WriteProfile(const ProfileResult& result)
    {
        if (m_ProfileCount++ > 0)
            m_OutputStream << ",";

		std::string name = result.Name;
		std::replace(name.begin(), name.end(), '"', '\'');

        m_OutputStream << "{";
        m_OutputStream << "\"cat\": \"function\", ";
        m_OutputStream << "\"dur\": " << (result.End - result.Start) << ", ";
        m_OutputStream << "\"name\": \"" << name << "\", ";
        m_OutputStream << "\"ph\": \"X\", ";
        m_OutputStream << "\"pid\": 0, ";
        m_OutputStream << "\"tid\": 0, ";
        m_OutputStream << "\"ts\": " << result.Start;
        m_OutputStream << "}";

		m_OutputStream.flush();
	}

    void WriteHeader()
    {
		m_OutputStream << "{\"otherData\": {}, \"traceEvents\": [";
		m_OutputStream.flush();
    }

    void WirteFooter()
    {
        m_OutputStream << "]}";
        m_OutputStream.flush();
    }

    static Instrumentor& Get()
    {
        static Instrumentor* instance = new Instrumentor();
        return *instance;
	}
};

// Instrumentation的意思是 注入我们的代码进行分析
class InstrumentationTimer
{
public:
    InstrumentationTimer(const char* name)
		: m_Name(name), m_Stopped(false)
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    void Stop()
    {
        auto endTimepoint = std::chrono::high_resolution_clock::now();

        auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        auto end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        std::cout << m_Name << ": " << (end - start) << "μs)\n";
        
		Instrumentor::Get().WriteProfile({ m_Name, start, end });

        m_Stopped = true;
    }

    ~InstrumentationTimer()
    {
        if(!m_Stopped)
            Stop();
    }

private:
	const char* m_Name;
	bool m_Stopped;
    std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
};

// 需要测试性能的函数
void Function1()
{
    InstrumentationTimer timer("Function1");

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World!" << std::endl;
}

void Function2()
{
    InstrumentationTimer timer("Function2");

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World! #" << sqrt(i) << std::endl;
}

int main()
{
	// BeginSession和EndSession之间做的事情 将会被放入特定分析文件中
    // 这样就可以把需要分析的数据分解成多个文件 使用Session的目的就是这个
	Instrumentor::Get().BeginSession("Profile");
	Function1();
	Function2();
	Instrumentor::Get().EndSession();

	std::cin.get();
}
```

得到json文件 和vcxproj在同一个目录里

```c++
{"otherData": {}, "traceEvents": [{"cat": "function", "dur": 74437, "name": "Function1", "ph": "X", "pid": 0, "tid": 0, "ts": 292041206544},{"cat": "function", "dur": 338314, "name": "Function2", "ph": "X", "pid": 0, "tid": 0, "ts": 292041281690}]}
```

把我们得到的json文件 在chrome tracing中load

如果发现它的计时单位是微秒 那么InstrumentationTimer类的Stop函数里的start和end 就应该写microseconds 而不是milliseconds 如果计时单位是毫秒 就正确了

点击可视化出来的方块 就可以在左下角看到

```c++
Title	Function1
Category	function
User Friendly Category	other
Start	0.000 ms
Wall Duration	71.095 ms
```

```c++
Title	Function2
Category	function
User Friendly Category	other
Start	71.463 ms
Wall Duration	263.883 ms
```

添加一个函数

```c++
void RunBenchmarks()
{
    InstrumentationTimer timer("RunBenchmarks");

    std::cout << "Running Benchmarks...\n";
    Function1();
    Function2();
}
```

就在chrome tracing上发现是RunBenchmarks 又分成了两块Function1 Function2

```c++
#define PROFILING 1
#if PROFILING
#define PROFILE_SCOPE(name) InstrumentationTimer timer##__LINE__(name)
// 拼接了行号 这样就可以为变量取一个唯一的名字 可以不需要## 这取决于编译器的使用 安全起见还是用了
#define PROFILE_FUNCTION() PROFILE_SCOPE(__FUNCTION__)
// 这个宏会调用PROFILE_SCCOPE宏 把函数的名字__FUNCTION__作为name 预处理器替你完成
#else
#define PROFILE_SCOPE(name)
#endif

// 需要测试性能的函数
void Function1()
{
	PROFILE_FUNCTION(); // 更先进的做法
    // PROFILE_SCOPE("Function1"); // 旧的做法

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World!" << std::endl;
}
```

但如果是有重载的函数 有相同的函数名 但是接收的参数不同 有不同的函数签名

```c++
void PrintFunction(int value)
{
	PROFILE_FUNCTION();

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World!" << std::endl;
}

void PrintFunction()
{
    PROFILE_FUNCTION();

	for (int i = 0 ; i < 1000; i++)
		std::cout << "Hello World! #" << sqrt(i) << std::endl;
}

void RunBenchmarks()
{
    PROFILE_SCOPE("RunBenchmarks");

    std::cout << "Running Benchmarks...\n";
    PrintFunction(39);
    PrintFunction();
}
```

运行RunBenchmarks函数时 因为预处理器\_\_FUNCTION\_\_取的是函数的实际名称 也就是PrintFunction

我们想要函数签名 也就是\_\_FUNCSIG\_\_ 

```c++
#define PROFILING 1
#if PROFILING
#define PROFILE_SCOPE(name) InstrumentationTimer timer##__LINE__(name)
#define PROFILE_FUNCTION() PROFILE_SCOPE(__FUNCSIG__)
#else
#define PROFILE_SCOPE(name)
#endif
```

这样展示出来的就不是函数名 而是函数签名

```c++
namespace Benchmark {
    void PrintFunction(int value)
    {
        PROFILE_FUNCTION();

        for (int i = 0; i < 1000; i++)
            std::cout << "Hello World!" << std::endl;
    }

    void PrintFunction()
    {
        PROFILE_FUNCTION();

        for (int i = 0; i < 1000; i++)
            std::cout << "Hello World! #" << sqrt(i) << std::endl;
    }

    void RunBenchmarks()
    {
        PROFILE_SCOPE("RunBenchmarks");

        std::cout << "Running Benchmarks...\n";
        PrintFunction(39);
        PrintFunction();
    }
}
```

可以放在命名空间中 调用时就用`Benchmark::RunBenchmarks();`

# 单例模式 singleton

不是C++语言特性 而是一种设计模式

单例是一个类的单一实例 只想实例化一次 但是单例真的需要一个类吗 C++并不强制使用类 它允许函数不属于任何类 它并不是像java C#那样 所有东西都必须是一个类 

单例类大概就像命名空间 C++中的单例 只是一种组织一堆全局变量和静态函数的方式

[singleton的static](#mypoint_19)

```c++
class Singleton
{
public:
	// 静态访问该类 GetInstance() 或者简写为 Get() 单例类只有一个实例 所以返回那个实例的引用
	static Singleton& GetInstance() // 这是一个静态方法 它就是Singleton::GetInstance() 只能调用静态变量 但是s_Instance就是静态变量
	{
		return s_Instance;
	}

	void Function() {}

private:
	Singleton() {}; // Singleton不能有public的构造函数 否则就会允许被实例化 此处意味着该类不能再外部被实例化
	
	static Singleton s_Instance; // 在private 只创建一次单例类的静态实例
};

// 静态成员变量必须在类外定义
Singleton Singleton::s_Instance;

int main()
{
	// 通过GetInstance()来访问这个单例 Singleton::GetInstance()就是那个单例
	Singleton& instance = Singleton::GetInstance(); // 一定要用引用 而不是复制
	// 假如这个实例想调用什么函数
	Singleton::GetInstance().Function();
	instance.Function(); // 和上面那句的含义是一样的
}
```

其实我们只是制作了一个名叫Singleton的类 **C++并不能产生任何约束 所以说这只是一种设计模式 而不是一种语法** 它能创建单例 是因为我们把构造函数private了 把实例静态了 然后又把访问单例的方法静态了

如果主函数里写`Singleton instance = Singleton::GetInstance();` 是真的会发生复制 虽然构造函数是private 但**拷贝构造函数和赋值运算符如果没有被显式删除** 编译器会自动生成它们 所以这行代码会调用拷贝构造函数 通过复制创建一个新的Singleton实例 而不是返回原有的s_Instance

这样会破坏单例模式的初衷 单例的本意是全局只有一个实例 但如果允许拷贝 就会有多个实例

需要在public的开头写上`Singleton(const Singleton&) = delete;` 显式删除拷贝构造函数

```c++
// 随机数生成器
class Random
{
public:
	Random(const Random&) = delete;

	static Random& GetInstance()
	{
		return s_Instance;
	}

	float Float() { return m_RandomGenerator; }

private:
	Random() {};

	float m_RandomGenerator = 0.5f; // 就假装这个是我们用某种方式生成的随机数
	
	static Random s_Instance;
};

Random Random::s_Instance;

int main()
{
	float number = Random::GetInstance().Float(); // 这样就生成了一个随机数
}
```

使用单例类 就是因为它实际上是一个类 可以支持所有类特性 比如类成员变量

```c++
// 随机数生成器
class Random
{
public:
	Random(const Random&) = delete;

	static Random& GetInstance()
	{
		return s_Instance;
	}

	static float Float() { return GetInstance().IFloat(); } // 静态方法
private:
	float IFloat() { return m_RandomGenerator; } // 也可以用FloatImpl Impl是implementation 但是IFloat看起来更像一个接口 意思就是Internal内部的Float函数
	Random() {};

	float m_RandomGenerator = 0.5f;
	
	static Random s_Instance;
};

Random Random::s_Instance;

int main()
{
	float number = Random::Float(); // 就不需要再使用Random::GetInstance().Float()
}
```

现在还有一个问题是 类成员中的静态实例 需要在类外部初始化 于是它不能直接捆绑在类的内部 只能放到某个翻译单元(cpp文件)中 我们希望这个静态声明能在静态函数里 

把`Random Random::s_Instance;`删掉

```c++
class Random
{
public:
	Random(const Random&) = delete;

	static Random& GetInstance()
	{
        static Random instance;
		return instance;
	}

	static float Float() { return GetInstance().IFloat(); }
private:
	float IFloat() { return m_RandomGenerator; }
	Random() {};

	float m_RandomGenerator = 0.5f;
};
```

这是局部static 这个局部变量只有在类的方法里声明才有用 局部static只在作用域内生效 意思是只有这个方法才可以调用这个变量 而类的静态变量 就是要在类外部声明 对于整个类都可以使用 GetInstance被第一次调用时 instance将被实例化 生命期很长 只会创建一次 不会重复创建

完全可以不用这个单例类 而是把所有代码写在namespace里 但是使用类是更有条理的

# 小字符串优化 SSO

能允许速度慢的话 就不要用C++了 减少字符串的使用 就是减少内存分配 

STL对于小到一定程度的字符串 可以只分配一小块基于栈的缓冲区 而不是堆分配的 所以如果你有一个非常小的字符串 就不用考虑`const char*`或者试图微观管理 优化你的代码 因为STL本来就不会做堆分配

为了防止堆分配 可能你使用`const char* name = "Miku";` 但其实这里并没有堆分配 这符合C++的小字符串 只存储在一个静态分配的缓冲区 不会使用堆内存

右键代码中的std::string 查看定义 到达这一行 `using string  = basic_string<char, char_traits<char>, allocator<char>>;` 所以string其实是basic_string的 别名右键basic_string 转到定义 就到达了basic_string类

## 如何阅读STL源码

我们就以std::string为例 学习如何阅读STL源码 重点关注小字符串优化机制

右键头文件`#include <string>`的string 转到文档 就到达了`<string>`头文件 也就几百行 说明这其中是**没有具体实现**的 在代码中任何一处右键 - 大纲显示 - 折叠到定义 就可以看到这里是一些全局函数 比如`getline` `stoi` `to_string`

回到文件开头 可以看到include了一些头文件 `<xstring>` 约定x前缀表示核心容器实现 右键转到文档 这份文件有5000多行 继续右键折叠到定义

ctrl+F 打开匹配大小写 搜索`class string` 没有找到 搜索string 看到了非常多的`basic_string_view` `basic_string` 直到我们看到了一行`_EXPORT_STD using string  = basic_string<char, char_traits<char>, allocator<char>>;` 于是我们知道 string就是basic_string的别名 于是继续搜索`class basic_string` 发现这是一个2000多行的类 就是我们要找的核心实现 很多STL的核心实现都是以`basic_`命名

```c++
_EXPORT_STD template <class _Elem, class _Traits = char_traits<_Elem>, class _Alloc = allocator<_Elem>>
class basic_string {

}
```

这是一个模板类 拿到任何一个类 我们都需要查看

1. 核心成员变量
2. 构造函数 析构函数
3. 内存管理策略
4. 常用操作

可以先按ctrl+K ctrl+K 为这个basic_string类添加一个书签

往下看 找到一个不接收任何参数的构造函数

```c++
basic_string() noexcept(is_nothrow_default_constructible_v<_Alty>) : _Mypair(_Zero_then_variadic_args_t{}) {
        _Mypair._Myval2._Alloc_proxy(_GET_PROXY_ALLOCATOR(_Alty, _Getal()));
        _Tidy_init();
    }
```

不禁要问 `_Mypair`是什么 右键`_Mypair` 速览定义

```c++
_Compressed_pair<_Alty, _Scary_val> _Mypair;
```

用同样的方式查看`_Compressed_pair`类 注释里写store a pair of values, deriving from empty first 在本例中它存储了一对`_Alty` `_Scary_val` 我们对`_Scary_val`右键速览定义 发现是`_String_val`的别名

**\_String_val**是一个类 是实现小字符串优化的核心

```c++
class _String_val : public _Container_base {
public:
    using value_type      = typename _Val_types::value_type;
    using size_type       = typename _Val_types::size_type;
    using difference_type = typename _Val_types::difference_type;
    using pointer         = typename _Val_types::pointer;
    using const_pointer   = typename _Val_types::const_pointer;
    using reference       = value_type&;
    using const_reference = const value_type&;

    _CONSTEXPR20 _String_val() noexcept : _Bx() {}

    // length of internal buffer, [1, 16] (NB: used by the debugger visualizer)
    static constexpr size_type _BUF_SIZE = 16 / sizeof(value_type) < 1 ? 1 : 16 / sizeof(value_type);
    // roundup mask for allocated buffers, [0, 15]
    static constexpr size_type _Alloc_mask = sizeof(value_type) <= 1 ? 15
                                           : sizeof(value_type) <= 2 ? 7
                                           : sizeof(value_type) <= 4 ? 3
                                           : sizeof(value_type) <= 8 ? 1
                                                                     : 0;
    // capacity in small mode
    static constexpr size_type _Small_string_capacity = _BUF_SIZE - 1;

    _NODISCARD _CONSTEXPR20 value_type* _Myptr() noexcept {
        value_type* _Result = _Bx._Buf;
        if (_Large_mode_engaged()) {
            _Result = _Unfancy(_Bx._Ptr);
        }

        return _Result;
    }

    _NODISCARD _CONSTEXPR20 const value_type* _Myptr() const noexcept {
        const value_type* _Result = _Bx._Buf;
        if (_Large_mode_engaged()) {
            _Result = _Unfancy(_Bx._Ptr);
        }

        return _Result;
    }

    _NODISCARD _CONSTEXPR20 bool _Large_mode_engaged() const noexcept {
        return _Myres > _Small_string_capacity;
    }

    _CONSTEXPR20 void _Activate_SSO_buffer() noexcept {
        // start the lifetime of the array elements
#if _HAS_CXX20
        if (_STD is_constant_evaluated()) {
            for (size_type _Idx = 0; _Idx < _BUF_SIZE; ++_Idx) {
                _Bx._Buf[_Idx] = value_type();
            }
        }
#endif // _HAS_CXX20
    }

    _CONSTEXPR20 void _Check_offset(const size_type _Off) const {
        // checks whether _Off is in the bounds of [0, size()]
        if (_Mysize < _Off) {
            _Xran();
        }
    }

    _CONSTEXPR20 void _Check_offset_exclusive(const size_type _Off) const {
        // checks whether _Off is in the bounds of [0, size())
        if (_Mysize <= _Off) {
            _Xran();
        }
    }

    [[noreturn]] static void _Xran() {
        _Xout_of_range("invalid string position");
    }

    _NODISCARD _CONSTEXPR20 size_type _Clamp_suffix_size(const size_type _Off, const size_type _Size) const noexcept {
        // trims _Size to the longest it can be assuming a string at/after _Off
        return (_STD min)(_Size, _Mysize - _Off);
    }

    union _Bxty { // storage for small buffer or pointer to larger one
        // This constructor previously initialized _Ptr. Don't rely on the new behavior without
        // renaming `_String_val` (and fixing the visualizer).
        _CONSTEXPR20 _Bxty() noexcept : _Buf() {} // user-provided, for fancy pointers
        _CONSTEXPR20 ~_Bxty() noexcept {} // user-provided, for fancy pointers

        value_type _Buf[_BUF_SIZE];
        pointer _Ptr;
        char _Alias[_BUF_SIZE]; // TRANSITION, ABI: _Alias is preserved for binary compatibility (especially /clr)
    };
    _Bxty _Bx;

    // invariant: _Myres >= _Mysize, and _Myres >= _Small_string_capacity (after string's construction)
    // neither _Mysize nor _Myres takes account of the extra null terminator
    size_type _Mysize = 0; // current length of string (size)
    size_type _Myres  = 0; // current storage reserved for string (capacity)
};
```

我们将逐行分析

构造函数是`_String_val() noexcept : _Bx() {}` `_Bx`是一个`_Bxty` 在类的后半段可以看到

```c++
union _Bxty {
    _CONSTEXPR20 _Bxty() noexcept : _Buf() {} // 构造函数 初始化_Buf数组 即小缓冲区
    _CONSTEXPR20 ~_Bxty() noexcept {} // 析构函数

    value_type _Buf[_BUF_SIZE]; // 小缓冲区数组 类型为value_type 长度为_BUF_SIZE 用于存储较短字符串内容 实现小字符串优化
    pointer _Ptr; // 指针 用于当字符串较长时存储指向堆上分配的大缓冲区的指针
    char _Alias[_BUF_SIZE]; // 用于二进制兼容 暂时不用管
};
_Bxty _Bx;
```

这是一个联合体 官方有注释说 存储小的buffer或者指向更大buffer的指针 使用union可使让同一块内存可以用不同方式解释 实现小字符串优化

所以构造函数就是创建了一个空的名为`_Bx`的`_Bxty`类型union 那么它就会创建一个`_Buf[_BUF_SIZE]`数组 实际上也等同于创建了一个指针`_Ptr` 但我们还不知道`_BUF_SIZE` 所以在创建之前需要设置好`_BUF_SIZE` 而且我们也不知道union里的`_Ptr`从哪里来

于是回到类的开头 首先解决`_BUF_SIZE`的问题 实际上你可以通过双击高亮`_BUF_SIZE` 迅速定位到这里

```c++
static constexpr size_type _BUF_SIZE = 16 / sizeof(value_type) < 1 ? 1 : 16 / sizeof(value_type);

static constexpr size_type _Alloc_mask = sizeof(value_type) <= 1 ? 15
                                       : sizeof(value_type) <= 2 ? 7
                                       : sizeof(value_type) <= 4 ? 3
                                       : sizeof(value_type) <= 8 ? 1
                                                                 : 0;

static constexpr size_type _Small_string_capacity = _BUF_SIZE - 1; 
```

1. 第一句 `static constexpr size_type _BUF_SIZE = 16 / sizeof(value_type) < 1 ? 1 : 16 / sizeof(value_type);`
   - `sizeof(value_type)` 是这个类型的一个字符占用的字节数
   - `16 / sizeof(value_type)` 是16字节空间里能放下几个value_type类型的字符
     <1就是一个都放不进去 那就取1 否则就取实际能放进去的数目
     一个都放不进去却仍然取1 是为了前面那个联合体`_Bxty`成员`_Buf[_BUF_SIZE]`至少有一个元素 类型安全
2. 由于只要`sizeof(value_type)>=16` `_BUF_SIZE`就是1 所以第三句`static constexpr size_type _Small_string_capacity = _BUF_SIZE - 1;` 这时小字符串的容量就是0 实际上就会走长字符串分支 采用指针存储
3. 第二句那么复杂的长句 是用于内存分配时对齐 减少碎片

通过这几个操作 我们得到了`_BUF_SIZE` `_Alloc_mask` `_Small_string_capacity`

现在解决`_Ptr`的问题

```c++
_NODISCARD _CONSTEXPR20 value_type* _Myptr() noexcept {
    value_type* _Result = _Bx._Buf;
    if (_Large_mode_engaged()) {
        _Result = _Unfancy(_Bx._Ptr);
    }

    return _Result;
}

_NODISCARD _CONSTEXPR20 const value_type* _Myptr() const noexcept {
    const value_type* _Result = _Bx._Buf;
    if (_Large_mode_engaged()) {
        _Result = _Unfancy(_Bx._Ptr);
    }

    return _Result;
}

_NODISCARD _CONSTEXPR20 bool _Large_mode_engaged() const noexcept {
    return _Myres > _Small_string_capacity;
}
```

`value_type* _Result = _Bx._Buf;`
`_Result`是一个指针 `_Bx`是一个联合体 这个联合体要么是小字符串直接存 要么就是指向长字符串的指针 `_Bx._Buf`就是那个小字符串

而`if (_Large_mode_engaged())` 也就是`_Myres > _Small_string_capacity` `_Myres`表示当前字符串的容量 那么`_Result`就指向`_Bx._Ptr` `_Unfancy`通常是去掉智能指针

`_Myptr()`所做的事就是 字符串长度超过16 就会切换为指针 没超过就直接存

我们现在就要回到`basic_string` 看看哪里调用了`_Myptr()`

在构造函数之后 就可以看到一些常用的方法 比如重写的操作符

```c++
_CONSTEXPR20 basic_string& operator=(const _Elem _Ch) { // assign {_Ch, _Elem()}
    _ASAN_STRING_MODIFY(*this, _Mypair._Myval2._Mysize, 1);
    _Mypair._Myval2._Mysize = 1;
    _Elem* const _Ptr       = _Mypair._Myval2._Myptr();
    _Traits::assign(_Ptr[0], _Ch);
    _Traits::assign(_Ptr[1], _Elem());
    return *this;
}
```

`_Mypair`是`_Compressed_pair<_Alty, _Scary_val>` 那么`_Myval2`就是`_Scary_val` 而`_Myptr()`是`_Scary_val`的成员函数 所以`_Elem* const _Ptr = _Mypair._Myval2._Myptr();`就是获取字符串数据的指针 无论是直接存储的小字符串 还是堆分配的字符串

只要大于等于16个字节就会发生分配 可以重写operator new 在release模式下进行测试

# 跟踪内存分配

内存是非常重要的东西 知道你的程序什么时候分配内存 特别是堆内存 是很有用的
