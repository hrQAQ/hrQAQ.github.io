---
title: 「系统编程」Linux工具链
date: 2022-09-08 14:37:41
thumbnail: https://oss.horik.cn/blog/header-linux-tools.jpg
categories: 系统编程
tags:
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

这篇文章是对GNU工具链中的编译套件（gcc），调试工具（gdb），构建和管理工具（make）的常用功能陈列。其实网上已经有很多这种总结帖子了，按照不重复造轮子的原则，我其实不应该花时间来做这个总结的。但我这刚开博客坑没几天，好歹得有点产出吧（笑，写写这种东西也不费事儿，纯当写memo备忘了。另外，如果你想上手工具的话，最好的办法是先有个大概了解，心里有谱了就可以上手用了，需要用什么再查什么。事实上，读or写这种文档就像是学英语背牛津词典一样，味同嚼蜡 >\_< （当然也很感谢为中文文档做贡献的前辈）

## GCC Compiler

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
# 选项 -I 手动添加文件头路径
~$> gcc test.c –I../inc -o test
~$> gcc hello.c -lpthread -I /lib64/ -o hello
# 多文件编译--plan A
~$> gcc testfun.c test.c -o test
# 多文件编译--plan B
~$> gcc -c testfun.c    #将testfun.c编译成testfun.o
~$> gcc -c test.c       #将test.c编译成test.o
~$> gcc testfun.o test.o -o test    #将testfun.o和test.o链接成test
# 加载动态链接库
~$> gcc hello.c -lpthread -o hello
```

## GDB Debugger

### gdb功能介绍

Linux系统中包含了GNU 调试程序gdb，用来调试C和 C++程序的调试器。gdb 提供如下功能：

- 运行程序，设置所有的能影响程序运行的参数和环境。
- 控制程序在指定的条件下停止运行。
- 当程序停止时，可以检查程序的状态。
- 修改程序的错误，并重新运行程序。
- 动态监视程序中变量的值。
- 可以单步执行代码，观察程序的运行状态。

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

### gdb的启动

有两种方式启动gdb，介绍如下：

```shell
# 启动gdb 再选择需要调试的文件
# 先启动gdb后执行file filename命令
~$> gdb
(gdb) file filename

# 启动gdb的同时装入可执行的程序
~$> gdb filename
```

启动gdb后，就可以使用gdb的命令调试程序。

### gdb相关的命令

- 保留gdb命令日志

	如果你想把GDB命令输出到一个文件里，有以下的命令与此相关（如果你看不懂下面的命令，或许可以在这里找到一点帮助：[The Missing Semester of Computer Science ---- Linux 命令基础](https://blog.horik.cn/2022/09/04/%E3%80%8C%E7%B3%BB%E7%BB%9F%E7%BC%96%E7%A8%8B%E3%80%8D%E5%B8%B8%E7%94%A8Linux%E5%91%BD%E4%BB%A4#the-missing-semester-of-computer-science------linux-%E5%91%BD%E4%BB%A4%E5%9F%BA%E7%A1%80)）

	```shell
	(gdb) set logging on
	(gdb) set logging off
	(gdb) set logging file <filename>
	(gdb) set logging overwrite [on|off] //默认会追加到logfile里 
	(gdb) set logging redirect [on|off] //默认GDB输出会在terminal和logfile里显示，用redirect让它只在logfile里显示
	(gdb) show logging
	```

- 运行调试程序

	```shell
	(gdb) run	# 开始运行
	(gdb) start # 运行并停在main函数上
	(gdb) continue # 继续运行
	```

- 快照信息 checkpoint

	gdb可以在程序执行的过程中保留快照(状态)信息，称之为checkpoint，可以在进来返回到该处再次查看当时的信息，比如内存、寄存器以及部分系统状态。通过设置checkpoint，万一调试的过程中错误发生了但是已经跳过了错误发生的地方，就可以快速返回checkpoint再开始调试，而不用重启程序重新来过。（PS: 类似于游戏存档）
	
	```
	(gdb) checkpoint // 在当前位置设置快照
	(gdb) info checkpoint
	(gdb) restart checkpoint-id
	(gdb) delete checkpoint checkpoint-id
	```

- 断点 breakpoint

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

- 显示检查工具

	

## Make

