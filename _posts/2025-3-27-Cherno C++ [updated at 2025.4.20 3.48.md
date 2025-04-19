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
- [类](#类)
- [静态 Static](#静态-static)
  - [类或结构体外部的static](#类或结构体外部的static)
  - [类或结构体中的static](#类或结构体中的static)
  - [局部static](#局部static)
- [本文目录使用python脚本自动生成](#本文目录使用python脚本自动生成)

------


我们有很多尚未解决的问题 在那些文本里我使用了 *暂时* 这个词语 请使用网页搜索功能

2025/3/27

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
int function(){
	//随便写点什么代码
#include "EndBrace.h" //这里就可以用这个include来代替这里缺的}
```

这就是预处理器做的 你如果写了一个`#define INTEGER int` 那么预处理器就会把你代码里所有的INTERGER替换成int 预处理完其实是得到一个 `.i` 文件
```c++
//function.i
int function(){
	///随便写点什么代码
}
```

这就是 `.i` 文件里的样子 不过会比这个多一些编译器自动生成的注释

2025/3/29

`#if` 让我们包含或者排排除基于给定条件的代码

```c++
#if 1
int multiply(int a, int b){
    int result = a * b;
    return result;
}
#end if
```

在 `.i` 文件里就是

```c++
int multiply(int a, int b){
    int result = a * b;
    return result;
}
```

如果是

```c++
#if 0
int multiply(int a, int b){
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

2025/3/30

错误列表 C开头的错误代码 就是编译错误 LNK开头的错误代码 是链接错误

实际上程序的入口点并不一定是main函数 也可以在属性—链接器—高级—入口点 进行配置

“未解决的外部符号”报错 就是链接器找不到它需要的东西

<span id="mypoint_6"></span>如果在函数前面加一个 [static](#mypoint_7) 就说明这个函数只在当前cpp文件里会被使用 其它cpp文件里都不会用到 那么它就不用参与链接 其他cpp文件就不使用 

参数不对 返回类型不对 函数名不对 都会发生链接错误

2025/4/3

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

`#include "Log.h"` `#include <iostream>` 有些用" " 有些用< >

如果头文件在某个文件夹里 用< >告诉编译器搜索包含路径文件夹 " "是包含相对于当前文件的文件 比如我有一个Log.h在Log.cpp文件所在目录的上层目录下 可以用`#include "../Log.h"`去返回到当前文件的上级目录 如果用< >就没有相对于当前文件的了 只需要在其中一个包含目录里面就行了 暂时我们不讨论包含目录的问题 任何时候我们都可以用" " 而< >只用于编译器包含路径 " "可以做一切 但我们通常只用它在相对路径 主要还是用< > iostream也是一个文件 它只是没有扩展名 C++的设计者为了将C++标准库与C标准库进行区分才这样做 C标准库常常有.h扩展 但C++没有 可以在#include iostream那句话右键 转到文档iostream来查看代码 在文档标签页那里右键 就可以打开它所在的文件夹 或者复制它所在路径

# Debug

断点 和 读取内存

计算机总是对的 它报错的话 99.99%是你错了而不是它错了

2025/4/5

在代码的任何一行我们都可以设置断点 当程序进行到这一行时 就暂停 在整个项目中 它会挂起执行线程 我们暂停程序 然后看看它的内存中发生了什么 一个运行中的程序所需的内存是相当大的 包括你设置的每一个变量 包括要调用的函数 当你将程序中断后 内存数据实际上还在 能查看内存 对于诊断程序出问题的原因非常有用 通过查看内存可以看到每一个变量的值 这个变量不应该设置为这个值 肯定出错了 还可以单步逐行运行代码 

设置断点时 要处于debug模式 而不是release模式 想运行到哪一行停止就在哪里设置断点 然后点击本地windows调试器

step into 逐语句(F11) 意思是进入到这行代码的函数里面
step over 逐过程(F10) 意思是从当前函数跳到下一行代码
step out 跳出(Shift+F11)意思是跳出当前函数 回到调用这个函数的位置

```c++
int main() {
	Log("Hello World!");
	std::cin.get();
}
```

我们在第二行设置断点 然后step into 就会进入Log.cpp（Log函数所在的文件） 里的Log函数

```c++
void Log(const char* message) {
	std::cout << message << std::endl;
}
```

把鼠标悬停在 `Log(const char* message)` 的message上面 可以看到 0x00f29b30 "Hello World!" 只是设置了函数栈帧结构 message已经被设置成了Hello World!
然后我们step over 黄色箭头就跳到了Log函数的第二行 箭头的意思是这一句还没执行 但将要执行这一句了 现在Hello world还没有被打印出来 如果我们再按一次step over 就会发现Hello world已经被打印出来了 因为我们调用了std::cout 再step over 我们就回到了main函数 再按step over 黄色箭头就到达了main函数的第3行 再step over 在弹出窗口按enter 调试就结束了

再来一个例子

```c++
int main() {
	int a = 8;
	a++;
	const char* string = "Hello";

	for (int i = 0; i < 5; i++) {
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

> 这些东西在 GAMES104 的 note 2025/3/29也有所提及 是打开Piccolo源码时感到不解 查询DeepSeek得到 但这次才算深刻理解文件组织形式

我们快速写一个hello world程序 然后对整个项目进行生成 在输出窗口可以看到
```
生成开始于 0:54...
1>------ 已启动生成: 项目: Project_test, 配置: Debug x64 ------
1>Project_test.vcxproj -> C:\coding\alpha\C++\test\Project_test\x64\Debug\Project_test.exe
```

Debug x64 是因为我们在Debug x64模式下进行的生成
x64是64位 win64
x86是32位 win32

我们现在知道了exe文件所在位置 但是真正打开这个文件夹 却找不到Project_test.exe文件 因为你并没有仔细看 实际上你打开的是 C:\coding\alpha\C++\test**\Project_test\Project_test**\x64\Debug

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

然后点确认 我们在project_test项目那里右键—清理 删除许多旧文件 这样删除不彻底 还是手动去文件资源管理器把有debug和x64的文件夹都删除 重新build 现在exe文件就在 `C:\coding\alpha\C++\test\Project_test\bin\x64\Debug` 这个文件夹里同时还有`Project_test.pdb`和`Project_test.ilk` 许多中间文件都在`C:\coding\alpha\C++\test\Project_test\bin\intermediates\x64\Debug`

在project_test项目那里右键—属性 输出目录那里 在编辑的时候 最右侧有一个选项符号 展开 点击 <编辑...> 然后点击 宏>> 我们就可以看到很多 \$( ) 这种形式的东西 在上方空白方框里 搜索SolutionDir 可以看到在本例中的目录为 `C:\coding\alpha\C++\test\Project_test\` 在最后它是自带 \ 的 所以我们在设置输出目录和中间目录时 \$(SolutionDir) 与 bin 中间 不用写 \

# if 语句

2025/4/7

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

int main() {
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
else if ( ){
	//
}

//实际上等效于
else{
	if ( ){
		//
	}
}

//所以并没有真正的else if 只是将两个语句放在一行而已
//else if并不是C++的关键字 就只是先else 然后if
```

只有在前面的 if 失败后 才会触发 else 语句

我们可以尽量尝试不使用 if 语句或者类似的东西 也就是不用逻辑编程 不是去做一个比较然后通过分支语句来处理 这样做会很慢 要尽量使用数学计算代替

# 循环

2025/4/8

游戏循环 只要玩家还没有决定退出游戏 就需要对游戏状态更新 渲染 让角色持续保持移动状态 持续做所有的事情 一帧接一帧地

```c++
for (int i = 0; i < 5; i++){
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
for ( ; condition; ){
	Log("Hello World!");
	i++;
	if (!(i < 5))
		condition = false;
}
```

`for( ; true; ; )` 或者 `for( ; ; ; )` 这就是无限循环

```c++
int i = 0;
while (i < 5){
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
for (int i = 0; i < 5; i++){
    if ((i + 1) % 2 == 0)
        continue;
	Log("Hello World!");
    std::cout << i << std::endl;
}
//Hello World! 变成只在 i 为偶数时输出 i=0 i=2 i=4分别输出
```

```c++
for (int i = 0; i < 5; i++){
    if ((i + 1) % 2 == 0)
        break;
	Log("Hello World!");
    std::cout << i << std::endl;
}
//Hello World! 变成只在 i=0 时输出一次 就跳出循环
```

```c++
int main(){
    for (int i = 0; i < 5; i++){
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

2025/4/13-4/15 文件丢失 重写

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
void Increment(int x){
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
void Increment(int* x){
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
void Increment(int& x){
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

# 类

2025/4/17

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
void Move(Player& player, int xa, int ya){
    //xa ya是在x轴 y轴上Player移动的距离
    player.x += xa * player.speed;
    player.y += ya * player.speed;
}
```

`Player&` 要修改Player对象 所以要用引用传递

如果要调用这个函数 `Move(player, 1, -1);`

但实际上类可以包含函数 我们可以把move函数移动到类中 **类内的函数被称为方法**

```c++
class Player {
public:
	int x, y;
	int speed;

	void Move(int xa, int ya) {
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
struct Vec2{
    float x, y;
    
    void Add(const Vec2& other){
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

```c++
// Log类
// 这不是一份好的代码 但是是简单的代码

#include <iostream>

class Log {
public:
	const int LogLevelError = 0; // Error级别
	const int LogLevelWarning = 1; // Warning级别
	const int LogLevelInfo = 2; // Info级别
	// LogLevelXXX 只有XXX级别以上的日志会被打印出来

private:
	int m_LogLevel = LogLevelInfo;
	// 默认级别为Info 所有级别的日志都会被打印出来


public:
	void SetLevel(int level) { // 设置日志级别
		m_LogLevel = level;
	}
	
	void Error(const char* message) {
		if (m_LogLevel >= LogLevelError)
			std::cout << "[ERROR]: " << message << std::endl;
	}
	void Warn(const char* message) {
		if (m_LogLevel >= LogLevelWarning)
			std::cout << "[WARNING]: " << message << std::endl;
	}
	void Info(const char* message) {
		if (m_LogLevel >= LogLevelInfo)
			std::cout << "[INFO]: " << message << std::endl;
	}
};


int main() {
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

2025/4/19

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

int main(){
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

struct Entity {
	int x, y;
	//这里选用结构体是因为希望x y是public

	void Print() {
		std::cout << x << ", " << y << std::endl;
	}
};

int main() {

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
static void Print() {
    std::cout << x << ", " << y << std::endl;
}
```

它现在就完全不知道x y是什么 可以改成

```c++
static void Print(Entity e) {
    std::cout << e.x << ", " << e.y << std::endl;
}
```

这个方法 是非静态类方法在编译时的真实样子

```c++
static void Print() {
    std::cout << e.x << ", " << e.y << std::endl;
}
```

这个方法就是静态类方法使用非静态变量时的样子 所以报错 它不知道你是要访问哪个Entity的x y 每个实例的x y都是不一样的 你又没给它一个Entity的引用 即使对于静态方法调用时 你写着`e.Print();` 但实际上因为它是静态方法 等同于你写了`Entity::Print();` 所以它还是不知道要找哪个Entity的x y

## 局部static

声明一个变量 需要考虑两个问题 也就是**变量的生存期和作用域**

生存期指 在它被删除之前 它会在我们的内存中存在多久
作用域指 我们可以访问变量的范围 

**静态局部变量 生存期基本上相当于整个程序的生存期 但作用域只在这个函数内** 但其实它不一定非要在函数里 你可以在任何作用域里声明它 这里只是用函数举例 也可以是if语句之类的 所以函数作用域的static和类作用域的static没有太大区别 生存期基本是相同的 但是在类的作用域中 类中的任何东西都可以访问这个静态变量 但在函数作用域声明一个静态变量 它将是那个函数的局部变量 对类来说也是局部变量

```c++
void Function() {
	static int i = 0;
}
```

意思是 当我第一次调用函数时 变量i将被初始化为0 然后所有对函数的后续调用 不会再反复创建新的变量 

```c++
#include <iostream>

void Function() {
	static int i = 0;
	i++;
}

int main() {

	for (int j = 0; j < 10; j++) {
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

class Singleton {
private:
	static Singleton* s_Instance; // 那个单例实例的指针
public:
	static Singleton& Get() { // 获取那个单例实例 返回的是引用
		return *s_Instance;
	}

	void Hello() {}; // 总之是做什么事情的一个方法
};

Singleton* Singleton::s_Instance = nullptr; // 初始化单例实例的指针为nullptr

int main() {

	Singleton::Get().Hello(); // 单例实例调用了Hello方法


	std::cin.get();
}
```

上面这个是类的静态
如果使用局部静态 main函数不变 class Singleton会变成下面这样 功能是完全一样的

```c++
class Singleton {
public:
	static Singleton& Get() {
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

1:18:59







# 本文目录使用python脚本自动生成

2025/4/15

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
