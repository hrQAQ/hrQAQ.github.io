---
title: 「系统编程」Linux工具链
date: 2022-09-08 14:37:41
thumbnail: https://oss.horik.cn/blog/header-linux-tools.jpg
categories: 系统编程
tags:
    - gcc
    - gdb
    - Makefile
    - Linux基础
---

GNU 工程开发了大量用于 Unix 的自由软件工具，其中**GNU工具链**（英语：GNU toolchain）是一个包含了由GNU计划所产生的各种编程工具的集合，由自由软体基金会负责维护工作。这些工具形成了一条工具链，用于开发应用程序和操作系统。

![ [GNU头像] ](https://www.gnu.org/graphics/heckert_gnu.transp.small.png)

> Hint 1
>
> 自由软件意味着使用者有运行、复制、发布、研究、修改和改进该软件的自由。

> Hint 2
>
> “GNU”这个名字是“GNU's Not Unix”的递归首字母缩写词。“GNU”的发音为*g'noo*，只有一个音节，发音很像“grew”，但需要把其中的*r*音替换为*n*音。

这篇文章是对GNU工具链中的编译套件（gcc），调试工具（gdb），构建和管理工具（make）的常用功能陈列。其实网上已经有很多这种总结帖子了，按照不重复造轮子的原则，我其实不应该花时间来做这个总结的。但我这刚开博客坑没几天，好歹得有点产出吧（笑，写写这种东西也不费事儿，纯当写memo备忘了。另外，如果你想上手工具的话，最好的办法是先有个大概了解，心里有谱了就可以上手用了，需要用什么再查什么。事实上，读or写这种文档就像是学英语背牛津词典一样，味同嚼蜡（这是最后一篇重复造轮子的文章 >\_< ）

## GCC - 基于C/C++的Compiler

gcc的全称是GNU Compiler Collection,即GNU编译器套件.它的初衷是为了给GNU操作系统专门写一款编译器,以解决不同GNU系统间编译器混乱的问题，现在,它已经可以编译众多语言,例如C, C++, Objective-C, Fortran, Ada, Go.并且成为了C, C++编译器的首选。

gcc是编译器套件，它提供了编译的全过程工具。

![image-20220908161511330](https://self-image-storage.oss-cn-beijing.aliyuncs.com/PicGo/%E7%BC%96%E8%AF%91%E5%85%A8%E8%BF%87%E7%A8%8B.png)

### 编译的四个阶段

如上图所示，广义上的C语言编译过程分为4个阶段，gcc编译器套件也对应为了四个部件：

- 预处理器 cpp

	主要完成三项工作：将原文件中的头文件展开，宏替换，去注释

	```shell
	~$> gcc -E hello.c hello.i
	```

- 编译器 gcc

	将C语言代码翻译为汇编语言代码

	```shell
	~$> gcc -S hello.i hello.s
	```

- 汇编器 as

	将汇编语言代码翻译为二进制的可重定位文件

	```shell
	~$> gcc -c hello.s -o hello.o
	```

- 链接器 ld

	将函数库中的代码组合到目标文件中

	```shell
	~$> gcc hello.o -o a
	```

### gcc常用参数

有了以上四个阶段的认识以后，我们再来介绍gcc的常用参数

```shell
-o：指定生成的输出文件名字；
-E：仅执行编译预处理；
-S：将C代码转换为汇编代码；
-wall：显示警告信息；
-c：仅执行编译操作，不进行连接操作。
-I：给出头文件的目录路径
-g: 保留调试需要使用的信息，需要gdb调试的时候使用此选项
-O: 编译的优化等级，未指定时默认不优化，O0\O1\O2\O3优化效果逐步递增
-D name: 编译时宏定义。在编译时预定义名称为name的宏，源代码中的name被替换为1
-l：用来指定程序要链接的库，-l参数紧接着就是库名
-L: dir选项。该选项将dir添加到库文件搜索路径中
-static: 该选项表示在编译时强制使用对应的静态链接库
-shared: 该选项创建共享库。它所创建的动态库文件以.so后缀结尾。
-ansi: 等价于-std=c89。该选项指定源程序使用ISO C90标准
-std=: 它确定源程序中所使用的C语言标准
```

给出一些实例，如果你都能看明白就会用gcc了，如果存有疑问，请查看https://wangchujiang.com/linux-command/c/gcc.html

```shell
# 无选项编译链接
~$> gcc test.c
# 选项 -o
~$> gcc test.c -o test
# 选项 -E
~$> gcc -E test.c -o test.i
# 选项 -S
~$> gcc -S test.i
# 选项 -c
~$> gcc -c test.s
# 无选项链接
~$> gcc test.o -o test
# 选项 -O
~$> gcc -O1 test.c -o test
# 选项 -D
~$> gcc -DDEBUG -o debug debug.c
~$> gcc -DDEBUG=0 -o debug debug.c
# 选项 -I dir 将 dir 增加至头文件搜索路径
~$> gcc test.c –I../inc -o test
~$> gcc hello.c -lpthread -I /lib64/ -o hello
# 选项 -L dir 将 dir 增加至库文件搜索路径
# 选项 -I library 指定编译时搜索的库名
~$> gcc hello.c -L . -lpthread -I /lib64/ -o hello

# 多文件编译--plan A
~$> gcc testfun.c test.c -o test
# 多文件编译--plan B
~$> gcc -c testfun.c    #将testfun.c编译成testfun.o
~$> gcc -c test.c       #将test.c编译成test.o
~$> gcc testfun.o test.o -o test    #将testfun.o和test.o链接成test

# 生成静态链接库
~$>  cd test
~$>  gcc -c *.c
~$>  ar rcs libtest.a *.o

# -fPIC PIC 指 Position Independent Code
# -share 生成一个共享对象，可以与其他对象链接以形成可执行文件。
# 从源文件生成动态链接库
~$> gcc -fPIC -shared func.c -o libfunc.so
# 从目标文件生成动态链接库
~$> gcc -fPIC -c func.c -o func.o
~$> gcc -shared func.o -o libfunc.so
# 加载动态链接库
~$> gcc hello.c -lpthread -o hello
```

---

## GDB - 功能强大的Debugger

### gdb功能介绍

功能强大的程序调试器，**gdb命令** 包含在GNU的gcc开发套件中，是功能强大的程序调试器。GDB中的命令固然很多，但我们只需掌握其中十个左右的命令，就大致可以完成日常的基本的程序调试工作。

### 使用gdb的前置工作

在使用gdb调试器之前需要完成两项准备工作：

- 开启程序崩溃信息转储，将崩溃信息存储到core文件内

	> core文件生成路径:	默认在输入可执行文件运行**命令**的同一路径下（不是可执行文件的同一路径下）

	使用ulimit -c命令查看core文件的生成开关。若结果为0，则表示关闭了此功能，不会生成core文件。相关命令如下：

	```shell
	ulimit -c # 查看core文件的生成开关
	ulimit -c unlimited # 让程序在崩溃时产生core文件
	ulimit -c filesize # 限制core文件的大小（filesize的单位为kbyte）
	```

- 在开启gdb调试之前，需要在编译源程序的时候加上-g选项

	```shell
	gcc -g test.c func.c -o test.out
	```

在完成以上两项准备工作后，我们就可以使用gdb来对程序调试了

```shell
gdb test.out test.out-core # 进行调试
```

### gdb常用命令

这一部分的实例介绍直接看manual： https://wangchujiang.com/linux-command/c/gdb.html，下面补充一些manual上面没有的and写的不是特别详细的：

**gdb日志**

如果你想把GDB调试过程中的屏幕显示内容保存为日志，有以下的命令与此相关（如果你看不懂下面的命令，或许可以在这里找到一点帮助：[The Missing Semester of Computer Science ---- Linux 命令基础](https://blog.horik.cn/2022/09/04/%E3%80%8C%E7%B3%BB%E7%BB%9F%E7%BC%96%E7%A8%8B%E3%80%8D%E5%B8%B8%E7%94%A8Linux%E5%91%BD%E4%BB%A4#the-missing-semester-of-computer-science------linux-%E5%91%BD%E4%BB%A4%E5%9F%BA%E7%A1%80)）

```shell
(gdb) set logging on
(gdb) set logging off
(gdb) set logging file <filename>
(gdb) set logging overwrite [on|off] //默认会追加到logfile里 
(gdb) set logging redirect [on|off] //默认GDB输出会在terminal和logfile里显示，用redirect让它只在logfile里显示
(gdb) show logging
```

**快照信息 checkpoint**

gdb可以在程序执行的过程中保留快照(状态)信息，称之为checkpoint，可以在进来返回到该处再次查看当时的信息，比如内存、寄存器以及部分系统状态。通过设置checkpoint，万一调试的过程中错误发生了但是已经跳过了错误发生的地方，就可以快速返回checkpoint再开始调试，而不用重启程序重新来过。（PS: 类似于游戏存档）

```shell
(gdb) checkpoint # 在当前位置设置快照
(gdb) info checkpoint	# 查看所有快照点信息
(gdb) restart checkpoint-id
(gdb) delete checkpoint checkpoint-id
```

**断点 breakpoint**

在一个位置上设置断点，可以对应多个位置，gdb要自动在需要的位置插入断点。在动态库里也可以设置断点，不过其地址在加载后才能解析。 断点的设置有几种方法

```shell
(gdb) break
(gdb) b		//缩写
(gdb) break [Function Name]		//函数名
(gdb) break [File Name]:[Line Number]	//文件名的第几行 
(gdb) break [Line Number] 	//第几行
(gdb) break *[Address]	// 想在地址0x4007d9 上设定断点 eg: break *0x4007d9
(gdb) i line <filename>:<line number>	// 获取对应行在内存中的地址的方法
```

条件断点设置

```shell
(gdb) break [...] if [Condition]
(gdb) break [...] thread [Thread-id]

eg.
(gdb) break main if argc > 1
(gdb) break 180 if (string == NULL && i < 0)
(gdb) break test.c:34 if (x & y) == 1
(gdb) break myfunc if i % (j+3) != 0
(gdb) break 44 if strlen(mystring) == 0
(gdb) b 10 if ((int)$gdb_strcmp(a,"chinaunix") == 0)
(gdb) b 10 if ((int)aa.find("dd",0) == 0)
```

可以用info breakpoints来查看相应断点信息

```shell
info breakpoints
```

**变量打印 print**

print接受表达式和计算它的值。任何该语言支持常值，变量和操作符都可以使用，像条件表达式，函数调用，类型转换，字符常量。GDB还支持数组常量，语法是{element, element...}, 比如print {1,2,3}

```shell
p[/fmt] <变量名称>

# fmt是print变量的格式
```

**设置程序中断时的观测变量 display**

设置程序中断后欲显示的数据及其格式。如果你发现你经常要打印某个表达式，你可以把它加入到“automatic display list”。每次程序停止时，都会显示

```shell
display expr
display/fmt expr
display/fmt addr
undisplay <dnums>  delete display dnums
disable display dnums
enable display dnums

# 例如，如果希望每次程序中断后可以看到即将被执行的下一条汇编指令，可以使用命令
display /i $pc
# 其中 $pc 代表当前汇编指令，/i 表示以十六进行显示。当需要关心汇编代码时，此命令相当有用。 undispaly，取消先前的display设置，编号从1开始递增。
```

**监视变量变化，并且在变化时停止程序 watch**

监视点是监视特定内存位置、特定表达式的值是否改变而触发程序暂停执行，而不用去关心该值到底在代码的哪个位置被修改的。监视的表达式可以是：某个变量的引用、强制地址解析(比如(int )0x12345678，你无法watch一个地址，因为地址是永远也不会改变的)、合理的表达式(比如a-b+c/d，gdb会检测其中引用的各个变量)。 

```shell
watch [-l|-location] expr [thread thread-id] [mask maskvalue]

# -location会让gdb计算expr的表达式，并将计算的结果作为地址，并探测该地址上的值

```

watch命令还存在两个变体：读监视点rwatch当expr被程序读的时候触发中断；读写监视点awatch会在程序读取或者写入expr的时候被中断

**查看调用栈 bt**

```shell
(gdb) bt
#0 level0 () at recursion.cpp:5
#1 0x08048462 in test (level=0) at recursion.cpp:17
#2 0x0804845b in test (level=1) at recursion.cpp:14
#3 0x0804845b in test (level=2) at recursion.cpp:14
#4 0x0804845b in test (level=3) at recursion.cpp:14
#5 0x0804845b in test (level=4) at recursion.cpp:14
#6 0x0804845b in test (level=5) at recursion.cpp:14
#7 0x08048479 in main () at recursion.cpp:22

(gdb) bt -2
#6 0x0804845b in test (level=5) at recursion.cpp:14
#7 0x08048479 in main () at recursion.cpp:22
```

**帧信息 frame**

frame用来显示当前帧的信息 基本语法是

```shell
frame
frame [Frame number]
f
info|i frame # 相比直接的frame，这个命令输出更详细的stack frame信息
```

**查看源代码 list \*$pc***

```shell
list *$oc
info line
```

**程序信息查看 info**

```shell
info registers | i reg
info <variables> | i <var> # 可以用 i var 来查看某个全局或者静态变量在哪个文件定义的
info locals # 显示当前帧的函数的ju'bu
info args # 显示当前帧的函数的参数， 配合frame，up和down来使用
info func # 显示所有的函数名
info files # 显示所有函数名
info break # 显示断点列表，包括每个断点到达的次数
```

**设置变量值 set**

gdb可修改运行时的参数，并使该变量按照用户当前输入的值继续运行。

```shell
# 在单步执行的过程中，键入命令：
set 变量＝设定值
```

---

## Make - GNU的工程化编译工具

> 无论是在linux 还是在Unix环境 中，make都是一个非常重要的编译命令。不管是自己进行项目开发还是安装应用软件，我们都经常要用到make或make install。利用make工具，我们可以将大型的开发项目分解成为多个更易于管理的模块，对于一个包括几百个源文件的应用程序，使用make和 makefile工具就可以简洁明快地理顺各个源文件之间纷繁复杂的相互关系。
>
> 而且如此多的源文件，如果每次都要键入gcc命令进行编译的话，那对程序员 来说简直就是一场灾难。而make工具则可自动完成编译工作，并且可以只对程序员在上次编译后修改过的部分进行编译。
>
> 因此，有效的利用make和 makefile工具可以大大提高项目开发的效率。同时掌握make和makefile之后，您也不会再面对着Linux下的应用软件手足无措了。

make是一个自动化的程序自动维护工具。它根据Makefile所描述的“依赖关系”自动决定项目的那些部分需要重新编译。

它的维护是基于依赖推导的，也就是说如果某个源程序文件被修改，那么依赖这个源程序文件的所有目标文件，都需要重新编译。如果仅修改了某几个源文件，则只重新编译这几个源文件，如果某个头文件被修改了，则重新编译所有包含该头文件的源文件

### make 执行步骤

1. 读入所有的Makefile。

2. 读入被include的其它Makefile。

3. 初始化文件中的变量。

4. 推导隐式规则，并分析所有规则。

5. 为所有的目标文件创建依赖关系链。

6. 根据依赖关系，决定哪些目标要重新生成。

7. 执行生成命令

### makefile 书写规则

阅读以下示例即可

```makefile
# main是target，即make最终需要创建的文件，如可执行文件和目标文件；
# main.o hello1.o hello2.o 是这个target的dependency 通常是编译目标文件所需要的其他文件
main:main.o hello1.o hello2.o
	# 下面是生成main的command，是make执行的动作，通常是把指定的相关文件编译成目标文件的编译命令，每个命令占一行，且每个命令行起始字符必须为TAB字符
	gcc -o main main.o hello1.o hello2.o 
	
main.o:main.c hello1.h hello 2.h 
	gcc -c main.c

hello1.o: hello 1.c hello 1.h 
	gcc -c hello1.c 

hello2.o: hello2.c hello2.h 
	gcc -c hello2.c

# clean是target target也可以是要执行的动作，如clean
clean:
	rm main hello1.o hello2.o main.o
```

执行上面这个makefile

```shell
make | make main	# 生成main
make target 	# 选择目标
make clean	# 执行清理
```

```makefile
# 伪目标
all: prog1 prog2 prog3

prog1: prog1.o utils.o
	gcc -o prog1 prog1.o utils.o
	
prog2: prog2.o
	gcc -o prog2 prog2.o
	
prog3: prog3.o sort.o utils.o
	gcc -o prog3 prog3.o sort.o utils.o
	
# 声明clean是伪目标，防止有clean文件存在使得makefile认为此命令无需执行而使得make clean失效
# 原因：根据依赖关系发现clean命令依赖的内容未发生变化
.PHONY: clean
clean:
        rm *.o temp
```

```makefile
# 隐式规则
# 根据后缀为.c的源文件使用gcc命令自动更新或者产生同名的.o文件，规则中略去产生目标文件的命令。
# 同时，目标所依赖的文件列表中的.c文件亦可省略
hello2.o: hello2.h

# 与上面的简化版等价
hello2.o: hello2.c hello2.h 
	gcc -c hello2.c
```

### makefile 自定义变量

makefile中的变量主要是为了简化文件，减少重复内容的书写，提高文件可读性。变量的机理是类似宏展开的方式。我们一般使用变量完成以下工作：

- 存储文件名列表
- 存储可执行文件名
- 存储编译器标识
- 存储参数列表

```makefile
# \ 代表本行内容尚未结束，下一行继续写，用于缩短行的长度，增加程序可读性
# objeckts变量存储了文件名列表，减少了重复
objects = main.o kbd.o command.o display.o \ 
	insert.osearch.o files.o utils.o

edit : $(objects)
 	cc -o edit $(objects)
 
main.o : main.c defs.h
	cc -c main.c

kbd.o : kbd.c defs.h command.h
	cc -c kbd.c

...

clean :
	rm edit $(objects)
```

```makefile
obj= main.o hello1.o hello2.o

main: $(obj)
	gcc -o main $(obj)
```

### makefile 内部变量

```makefile
# 当前规则的目标文件名
$@

# 成依赖列表中第一个依赖文件
$< 

# 比目标文件更新的以空格分隔的整个依赖的列表
$?

# 以空格分隔的所有的依赖文件
$^：

# 与$^功能相似，但包含有重复的依赖文件
$+
```

自动变量简化以后的makefile

```makefile
main:main.o hello1.o hello2.o
	gcc -o $@ $^

main.o:main.c hello1.h hello 2.h
	gcc -c $< 

hello1.o: hello 1.c hello 1.h 
	gcc -c $< 

hello2.o: hello2.c hello2.h 
	gcc -c $<

clean:
	rm main hello1.o hello2.o main.o
```

