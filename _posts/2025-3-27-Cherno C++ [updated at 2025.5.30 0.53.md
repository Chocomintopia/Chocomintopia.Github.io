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

`*ptr`是逆向引用指针 dereferencing the pointer 意思是这个指针所指的那个变量 这个地址上所在的那个变量
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

单例类 Singleton 只有一个实例的类

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
>    你可以用 但你得时刻记着这是属于我的 这里铭刻着我的名字
> 3. 作用域内持续长时间地使用 作用域之外不可见
>    明明一直占着存储空间却不被人发现喵

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

也可以用在lambda中

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

lambda表达式 匿名函数 []是lambda的捕获列表 用于控制lambda如何访问外部作用域的变量

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

写一个new int 需要4个字节的内存 就需要寻找4个字节内存的连续块 但并不是一行一行搜索内存看有没有4字节连续内存   而是有空闲列表 会维护那些有空闲字节的地址 暂时不过多讨论 如果找到了 它就返回一个指向这个内存的指针 这样就可以开始使用了

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

new其实是一个操作符 就像 + - = 所以可以重载这个操作符 其实只是类似一个函数 分配一定大小的内存 然后返回空指针 `void*` 一个没有类型的指针 指针只是一个内存地址 指针之所以需要类型 是因为你需要类型才操纵它 知道需要从这个地址开始读取多长的内存 但其实指针只是一个内存地址 一个数字 所以可以根本不需要什么类型 

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

`std::shared_ptr<Entity> sharedE2(new Entity());` 不能用这种写法 因为shared_ptr需要分配另一块内存 叫做控制块 用来存储引用计数 如果你已经分配好了一块new Entity 再传递给shared_ptr的构造函数 它就一共要做两次内存分配 先是new Entity的分配 又要分配shared_ptr的控制内存块 但如果用make_shared 就能把这两件事组合起来 而且**既然已经利用智能指针舍弃了new和delete 就不要再出现 但实际上它并没有真正取代new和delete**

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

也可以使用 **for循环的语法糖**

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

# std::vector使用优化

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

按照[Visual Studio设置](#Visual Studio设置)修改输出目录和中间目录 以及创建src文件夹

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





# 生成文件列表

打开目标文件夹 shift+右键 选择在此处打开powershell窗口 输入命令`Get-ChildItem -Name`

若需树状结构 安装tree命令后使用`tree`

# 本文目录使用python脚本自动生成

```python
import re
import sys

def generate_toc(content):
    """生成目录内容，不直接修改文件"""
    # 将文件内容按行分割为列表
    lines = content.split('\n')
    toc = []  # 存储生成的目录项
    in_code_block = False  # 标记当前是否在代码块内

    for line in lines:
        # line.strip()：去掉行首和行尾的空白字符（包括换行符、空格、制表符等）
        stripped_line = line.strip()

        # 检测代码块的开始/结束标记（```）
        if stripped_line.startswith('```'):
            # 反转代码块状态：遇到代码块开始标记设为True，结束标记设为False
            in_code_block = not in_code_block
            continue  # 跳过代码块标记行本身

        # 如果当前在代码块内，跳过所有处理
        if in_code_block:
            continue

        # 使用正则表达式移除行内代码（反引号包裹的内容）
        # `[^`]*`：匹配反引号开始，中间包含非反引号字符，直到反引号结束
        line_clean = re.sub(r'`[^`]*`', '', line)

        # 使用正则表达式严格匹配 Markdown 标题语法
        # ^#{1,6}：匹配以1到6个#开头的行
        # \s：要求#后面必须有一个空格（排除C++宏定义）
        # (?!目录)：排除中文"目录"关键词
        # [^\s]：确保标题内容不是空白字符
        if re.match(r'^#{1,6}\s(?!目录)[^\s]', line_clean.strip()):
            # 统计标题层级：通过计算#的数量确定（1-6）
            level = line_clean.count('#')
            # 提取标题文本：移除开头的#并去除两端空白
            title = line_clean.replace('#', '', level).strip()

            # 跳过所有形式的目录标题（中文/英文）
            if title.lower() in ['目录', 'contents']:
                continue

            # 根据标题层级生成缩进：每级缩进2个空格
            indent = '  ' * (level - 1)
            
            # 生成锚点链接：
            # 1. 使用正则表达式过滤特殊字符：[^\w\u4e00-\u9fff\- ] 
            #    - \w：保留字母、数字、下划线
            #    - \u4e00-\u9fff：保留中文字符
            #    - \-：保留连字符
            #    - 空格：保留用于后续转换
            # 2. 转换为小写：.lower()
            # 3. 空格转连字符：replace(' ', '-')
            # 4. 处理连续连字符：replace('--', '-')
            anchor = re.sub(r'[^\w\u4e00-\u9fff\- ]', '', title)
            anchor = anchor.strip().lower().replace(' ', '-').replace('--', '-')
            
            # 生成目录项文本，格式示例：
            #   - [标题内容](#锚点链接)
            toc.append(f"{indent}- [{title}](#{anchor})")
    
    # 组合最终目录内容：
    # 1. 添加# 目录标题
    # 2. 拼接所有目录项
    # 3. 添加水平分割线\n\n---\n\n
    return '# 目录\n' + '\n'.join(toc) + '\n\n------\n\n'

def update_file_content(md_file, new_toc):
    """执行文件更新操作"""
    # 读取文件全部内容
    with open(md_file, 'r', encoding='utf-8') as f:
        content = f.read()
    
    # 正则表达式匹配旧目录结构：
    # ^#{1,6}\s*目录：匹配任意层级的目录标题（如# 目录、##目录）
    # [\s\S]*?：匹配任意字符（包括换行符），非贪婪模式
    # ^------\n：匹配水平分割线
    pattern = r'(^#{1,6}\s*目录\s*[\s\S]*?)^------\n'
    # re.MULTILINE：使^匹配每行开头
    updated_content = re.sub(pattern, new_toc, content, flags=re.MULTILINE)
    
    # 如果没有找到旧目录，直接在文件开头插入新目录
    if updated_content == content:
        updated_content = new_toc + content
    
    # 写入更新后的内容
    with open(md_file, 'w', encoding='utf-8') as f:
        f.write(updated_content)

if __name__ == "__main__":
    # 命令行参数验证
    if len(sys.argv) < 2:
        print("请通过命令行运行：python toc.py 你的文件.md")
        sys.exit(1)  # 非正常退出
    
    md_file = sys.argv[1]  # 获取输入文件路径
    
    try:
        # 读取文件内容（使用utf-8编码保证中文兼容性）
        with open(md_file, 'r', encoding='utf-8') as f:
            content = f.read()
        
        # 生成新目录内容
        new_toc = generate_toc(content)
        
        # 显示生成的目录（\n实现换行排版）
        print("生成的目录：\n")
        print(new_toc)

        # 用户确认机制（.lower()统一处理大小写输入）
        choice = input("\n是否要替换文件中的旧目录？(y/n): ").lower()
        if choice == 'y':
            update_file_content(md_file, new_toc)
            print("目录已更新！")
        else:
            print("未修改文件")  # 用户取消操作
    
    # 异常处理（细化错误类型）
    except FileNotFoundError:
        print(f"错误：文件 {md_file} 不存在")
    except PermissionError:
        print(f"错误：没有文件写入权限")
    except Exception as e:
        print(f"发生未预期错误：{str(e)}")
```

在命令行中处理带有空格的文件路径的方法：

1. 引号包裹路径
   在路径两侧添加英文双引号 告诉命令行将整个路径视为一个整体
   `cd "C:\Program Files\My Project"`

4. 自动化工具中的路径处理在编程或脚本中 建议始终使用编程语言提供的路径处理函数（如 Python 的 `os.path`） 避免手动拼接路径
   
    ```python
    import os
      
    path = os.path.join("C:", "Program Files", "My Project")  # 自动处理路径分隔符和空格
    print(path)  # 输出：C:\Program Files\My Project
    ```
