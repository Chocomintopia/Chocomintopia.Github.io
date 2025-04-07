我们有很多尚未解决的问题 在那些文本里我使用了 *暂时* 这个词语 请使用搜索功能

# Cherno C++ 

2025/3/27

只有main函数可以没有返回值 默认返回0 其他函数都要有返回值 或者void

`cout << “Hello World” << endl`
实际上这个 << 是重载运算符 可以认为是个函数
是把hello world推到cout流中 然后在终端输出 endl是换行

### 编译

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

### 链接

2025/3/30

错误列表 C开头的错误代码 就是编译错误 LNK开头的错误代码 是链接错误

实际上程序的入口点并不一定是main函数 也可以在属性—链接器—高级—入口点 进行配置

“未解决的外部符号”报错 就是链接器找不到它需要的东西

如果在函数前面加一个 static 就说明这个函数只在当前cpp文件里会被使用 其它cpp文件里都不会用到 那么它就不用参与链接 其他cpp文件就不使用

参数不对 返回类型不对 函数名不对 都会发生链接错误

2025/4/3

函数或者变量 有相同的名字和相同的签名 也会发生链接错误

比如你写了一个头文件Log.h 在里面定义了一个函数 然后在两个cpp里都调用这个头文件 实际上就是把这个头文件复制到了两个cpp文件里 那么就是两个cpp文件里都写了这个函数的定义 定义重复了 如果两个cpp里都调用了头文件里的同一个函数 就会报链接错误 “未解决的外部符号”

解决方案：

1. 可以把这个函数定义为static `static void Log(const char* message)`这样这个函数被复制过去之后 就只在cpp文件内部生效 内部函数对于其他obj文件不可见 不会参与链接

2. 也可以把这个函数前面加上inline 意思是将函数调用替换为函数体 也就是比如 定义了函数体 `std::cout << message << std::endl;` 函数名为 `inline void Log(const char* message)` 这样实际上调用 `Log("Initialized Log");` 就等于是替换成了`std::cout << "Initialized Log" << std::endl;` 而并不复制函数到达cpp文件里 只要函数体

3. **（最佳）**把这个Log函数 不再写在Log.h里 而是写在Log.cpp里 Log.cpp被称为翻译单元 然后在Log.h里只保留Log函数的声明 `void Log(const char* message);` 不用static 也不用inline 这样链接之后 其他cpp文件仍然可以调用Log函数 但并不会重复 就不会链接错误

### 变量

int 4个字节byte 32位有符号 有一位表示符号 其余31位表示实际的数字 2^31^ 20多亿 这是正数的范围 但我们还需要表示负数和0 如果是无符号数unsigned 那就是从0到 2^32^ 

char 1个字节 short 2个字节 long 4个字节 long long 8个字节 但是到底几个字节都取决于编译器 我们可以调用`sizeof(long)` `sizeof(long long)`去查询 或者写`sizeof long`也行 这些数据类型也都可以变成unsigned 

char可以表示数字 也可以表示字符 这不是说其他整数类型不能表示字符 实际上字符也只是一个数字 根据ASCII码对应 但是根据编程习惯 我们一般期待char是一个字符 而其他整数类型代表的就应该是数字 `char a=65` 用cout对a进行输出 我们会得到字母A `char a='A'` 也是会输出A 因为cout就是会把变量a看成是一个字符 如果是`short a=65` 就会cout输出数字65 `short a='A'` 还是会cout输出65 **数据类型之间唯一的区别 就是分配多少内存的区别**

float 4个字节 double 8个字节 `float virable=5.5` 你以为你定义了一个float 实际上你定义了一个double `float virable=5.5f` 这才是真的float 或者`float virable=5.5F` 

<span id="mypoint_3"></span>bool true或者false 但是如果`bool virable=true` cout之后会输出数字1 因为实际上计算机不知道什么true还是flase 它只知道0和1 **0表示flase 任何不是0的数字都是1** 计算机只会处理数字 bool是1个字节 我们有巨大的1byte内存地址空间用来放1个bool值 我们不一定要确定是哪个bit被设置为1 只要这个byte里有东西 不为0 那它就是true 所以true有可能是1 但并不强迫我们设置为1 [关于在C++中 bool 非0为true 0为false的讨论](#mypoint_4)

但为什么bool不是1个bit 它确实是只用1bit 但当我们处理寻址内存时 我们没有办法寻址只有1个bit位的内容 我们只能访问字节 但你也可以在1byte内存里存储8个bool数 但仍然是分配1个字节的内存

可以用这些基本数据类型 写我们自己的自定义数据类型

### 函数

就是代码块 在class类里面 叫做方法 谈到函数时 我们明确地指不属于类里面的东西 你可以认为函数是有一个输入 也有一个输出 我们可以为函数提供一定的参数 当然也可以不提供参数 函数也可以不返回任何东西 就是void **函数是为了防止写重复代码的** 但也不用所有的东西都写成函数 会让程序变慢 每次我们调用函数时 编译器生成一个call指令 就会进入堆栈结构 把像参数这样的东西推进堆栈 还会需要一个返回地址 又会jump到二进制执行文件的不同位置 以便执行我们的函数指令 为了将push进去的结果返回 又要回到最初调用函数之前 就像在内存中跳跃来执行函数 跳跃和执行都需要时间 这些都是因为编译器决定保持我们的函数作为一个实际的函数 并不做内联inline 

### 头文件

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

### Debug

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

### Visual Studio设置

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

### if 语句

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

```
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

### 循环

2025/4/8
