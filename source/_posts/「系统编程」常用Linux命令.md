---
title: 「系统编程」常用Linux命令
date: 2022-09-04 01:10:12
categories: 系统编程
thumbnail: https://oss.horik.cn/blog/header-Linux%E7%BC%96%E7%A8%8B%E5%9F%BA%E7%A1%80.avif
tags:
    - Linux基础
---

这是一篇低创文章，主要是笔者在BUAA学习《系统编程》时的一些笔（zhai）记(chao)内容，大概率是写完就吃灰，或许等到期末或者以后记忆衰退了会掸去灰看看。

在本文你可以了解到

- Linux man手册基础阅读方法

- Linux常用命令及其常用参数的使用示例

本文不会谈论

- 高深的Linux命令使用技巧（喂，都说了这是一篇低创文章了，别期待太多捏）

- 完整的Linux命令参数介绍（因为我也看不懂）
- 特别贴合实际应用场景的使用范例（我也没咋用过，想不出来）

> **WARNING**
>
> 1. 笔者使用的是Unbuntu 20.04系统，由于安装时选择了中文系统，所以文中示例会有中英文夹杂的情况
> 2. 鉴于本文是男生自用99新笔记，内容会有很强个人性质，先提前叠个甲

如果到这里你还有兴趣往下翻翻，那我们就一起开始Linux世界的"Hello World"吧

---

## Man手册基本阅读方法

### 让我们从一个故事说起

> Read the F**king Manual（RTFM）

上帝用一天时间创造了Linux和系统命令，交付给人类使用还没几天，人类就给上帝发牢骚：“Linux好用是好用，但命令也太多了，每个命令的参数也多如牛毛，用的时候老是忘，忒不方便”，于是上帝又花了两天写出了指令Manual，告诉人类：“遇到不会的就来查Manual，知识都在里面了”。人类开始读Manual，熬了一宿，发现这玩意儿就不是人读的 : ( ，于是上帝又花了三天时间教会了人类怎么阅读Manual，就这样，人类终于能愉快地使用Linux系统了。

这个故事的本意并不是宣扬Manual有多么强大，事实上它还是很难读（互联网上仍然流传着古老的一句国骂——Read the F**king Manual），我想夹带的私货是一个软件交付时精力耗费的大小顺序：推广维护 >> 写文档 >> 开发软件，这一不等式的约束是如此稳固，以至于伟大的上帝也逃不开，所以一定不要当乙方（悲）

### man的自述

言归正传，那么伟大的Manual应该怎么读呢？让我们看看man对自己的介绍

```shell
# 命令行输入man man
$ man man
```

```shell
MAN(1)                                                                               手册分页显示工具                                                                              MAN(1)

名称
       man - 系统参考手册的接口

概述
       man [man 选项] [[章节] 页 ...] ...
       man -k [apropos 选项] 正则表达式 ...
       man -K [man 选项] [章节] 关键词 ...
       man -f [whatis 选项] 页 ...
       man -l [man 选项] 文件 ...
       man -w|-W [man 选项] page ...

描述
       man  是系统的手册分页程序。指定给 man 的 页 选项通常是程序、工具或函数名。程序将显示每一个找到的相关 手册页。如果指定了 章节，man 将只在手册的指定 章节 搜索。默认将按预定的顺序查
       找所有可用的 章节（参见 默认值 一节），并只显示找到的第一个 页，即使多个 章节 中都有这个 页面。

       下表显示了手册的 章节 号及其包含的手册页类型。

       1   可执行程序或 shell 命令
       2   系统调用(内核提供的函数)
       3   库调用(程序库中的函数)
       4   特殊文件(通常位于 /dev)
       5   文件格式和规范，如  /etc/passwd
       6   游戏
       7   杂项（包括宏包和规范）， 如 man(7)，groff(7), man-pages(7)
       8   系统管理命令(通常只针对 root 用户)
       9   内核例程 [非标准
====================后续省略====================
```

man是一个系统参考手册的入口.......啊再写下去就有水字数之嫌了，我不想做一个PPT内嵌的语音助手，请自行查看man的自述。

总而言之，man命令是一个帮我们从冗长的参考手册中找到想要的内容的工具，你可以把它看成字典的目录。但它和字典目录的用法不同，下面是它的命令用法。

```shell
# man常用命令格式
man [section number] [-k] command
```

呃，你貌似看不太懂，那就来补个课吧（其实看不懂的人就是我，乐）

### The Missing Semester of Computer Science ---- Linux 命令基础

#### Linux命令格式

linux命令一般长这个样子

```shell
command [option] [arguments]
```

偷用PPT上的一张图解释如下，前人之述备矣，我也不多费口舌了。

![image-20220902010737934](https://self-image-storage.oss-cn-beijing.aliyuncs.com/PicGo/Linux%E5%91%BD%E4%BB%A4%E6%A0%BC%E5%BC%8F%E8%A7%A3%E9%87%8A.png)

#### 参数Arguments的运用  

参数分为可选项参数和命令对象参数。两者的区别在于可选项参数是写命令的人预留给使用者的接口，用来调整命令内部的各种参数或配置的，而命令对象参数往往是Linux系统中的一些实体，是命令最终作用的对象，比如文件名、用户名、组。下面依旧是当一下PPT的搬运工：

- 可选项参数
	- 短格式可选项，如：-f 带参数的短格式可选项，如：-f512 或-f 512
	- 长格式可选项，如：--filesize
	- 带参数的长格式可选项，如：--filesize=512
- 命令对象参数
	- 文件名
	- 用户名、组名
	- …

> **命令格式的一些补充**（感觉这个格式和BNF文法有关联，但没细究过）：
>
> `...`：可变参数，参数可以写n多个
>
> `|`：或，左右两侧的内容选一个就行
>
> `[ab1A-F]`：匹配任何一个例举在集合中的字符
>
> `?`：匹配任何一个字符
>
> `*`：匹配零个或多个字符

#### 其他帮助性的命令

- Info

	> info command

	```shell
	$ info man
	```

- --help

	> command --help
	>
	> command -h

	```shell
	$grep --help
	```

####  --help, man, info的区别

直接搬运PPT讲解如下：

1. --help: 是一个工具选项,可以用来显示一些工具的信息。这些帮助信息是程序的作者加上去的，也就是说，这些信息是程序内部的。一般比man出来的要简单。

2. man : （manual）可以显示系统手册页中的内容，这些内容大多数都是对命令的解释信息。这些信息是操作系统文档里面的。如果没有文档，是不会显示这些帮助信息的。一般比help出来的要详细。

	(1) Space 键可以显示下一屏的文本信息；(2) q 键是退出。

3. info: 是一个基于菜单的超文本系统，是由GNU项目开发并由Linux发布。 info 工具包括一些关于Linux Shell 工具，GNU项目开发程序说明文档。

	(1) ？ 键可以列出info窗口中的相关命令；(2) Space 键可以进行滚动浏览

当然，以上的介绍肯定不足以覆盖你在阅读Manual时遇到的所有情况，不过，前面的区域，以后再来探索吧~

---

## Linux常用命令

> 无他，唯手熟耳

强烈推荐[Linux命令搜索引擎](https://wangchujiang.com/linux-command/)，一个优质的中文Linux命令手册，它在说人话，我哭死。

下面的内容主要是对PPT的整理，部分有所补充，写道一半发现了前面推荐的引擎，权做个mini版内容吧，期末复习的时候也许派的上用场。如果您想看完整用法、参数列表、更多例子的请移步上面推荐的搜索引擎。

### 目录操作指令

#### ls

```shell
# 列出所有文件
ls –a 

# 列出所有的目录
ls –d 

# 列出文件的详细信息
ls –l 	
```

#### cd

```shell
# 进入/usr/bin目录。
cd /usr/bin

# 进入用户的home directory。
cd ~ 

# 返回到用户主目录
cd 

# 表示进入上次更改目录之前的目录
cd - 
```

#### mkdir、rmdir

```shell
# 创建目录
mkdir directory-name

# 删除目录 使用rmdir时，要确保目录内已无任何文件，否则命令不成功
rmdir directory-name

# 递归删除目录，如果子目录删除后若其父目录为空，则将一同删除
rmdir -p directory-name/sub-dir-name
```

#### pwd  显示当前工作目录

```shell
# 语法
pwd
```

---

### 文件操作指令

#### cat 显示文本文件内容 

```shell
# 语法
cat filename...

# 默认输出到屏幕
cat file1 file2

# 重定向输出到文件
cat file1 file2 > file3
```

#### cp 拷贝文件

```shell
#  语法 （在使用cp时一定要有目的地才行）
cp filename target-place

# 在cp中也可以使用通配符，像*、?
cp /root/* /temp
```

#### mv 文件更名或搬移

```shell
# 语法
mv filename target-place(new-filename)

# 把现在所在的目录中的netscape文件移到/usr内
mv netscape /usr

# 把现在所在的目录中的netscape文件重命名为nets
mv netscape nets
```

#### rm 删除文件

```shell
# 语法
rm filename

# 删除本目录下file文件
rm file1
```

#### cp mv rm 的几个选项

```shell
-f 表示强制执行，如果目标文件存在则进行覆盖或者
不提示进行删除
-i 交互式的执行命令，上述情况下，提示用户。
-r 递归删除、拷贝。 mv命令没有该选项。
```

#### find 寻找文件

```shell
# 语法 (将文件系统内符合条件的文件列出来。用户可以指定文件名称、类别、时间、大小、权限等等)
find dir-name options

# 列举当前目录及其子目录下所有扩展文件名是c的文件
find . -name "*.c"

# 将匹配文件输出到标准输出
find . -print -name ".sy"

# 对匹配的文件执行该选项所给出的命令，命令的形式为'command' {} \;
find . -name "*.c" -exec ls –l {} \;
```

#### grep 在文件中查找字符串

```shell
# 语法
grep str filename

# 在 file1文件中找寻tigger字符串
grep tigger file1

# 在 file1文件中找寻 big tigger字符串
grep "big tigger" file1

# 查找不包含字符串的行
grep -v printf file1

# 将查找结果的行序号显示出来
grep -v -n printf file1
```

### 文件备份和压缩命令

首先要弄清两个概念：打包和压缩。打包是指将一大堆文件或目录变成一个总的文件；压缩则是将一个大的文件通过一些压缩算法变成一个小文件。

为什么要区分这两个概念呢？这源于Linux中很多压缩程序只能针对一个文件进行压缩，这样当你想要压缩一大堆文件时，你得先将这一大堆文件先打成一个包（tar命令），然后再用压缩程序进行压缩（gzip bzip2命令）。

#### tar

```shell
# 对文件目录进行打包备份 语法
tar [选项...] [FILE]...

# 参数-f是必须的
-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

# 示例
# 这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。
tar -cf all.tar *.jpg
# 这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。
tar -rf all.tar *.gif
# 这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。
tar -uf all.tar logo.gif
# 这条命令是列出all.tar包中所有文件，-t是列出文件的意思
tar -tf all.tar
tar -cf archive.tar foo bar  # 从文件 foo 和 bar 创建归档文件 archive.tar。
tar -tvf archive.tar         # 详细列举归档文件 archive.tar 中的所有文件。
tar -xf archive.tar          # 展开归档文件 archive.tar 中的所有文件。

# 将 /home/vivek/bin/ 目录打包，并使用 gzip 算法压缩。保存为 /tmp/bin-backup.tar.gz 文件。
tar -zcvf /tmp/bin-backup.tar.gz /home/vivek/bin/ 
```

**其实最简单的使用 tar 就只要记忆底下的方式即可：**

```shell
压　缩：tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称
查　询：tar -jtv -f filename.tar.bz2
解压缩：tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录
```

这里使用的压缩方式是bz2，更多的压缩算法使用方式详见https://wangchujiang.com/linux-command/c/tar.html

#### gzip

```shell
# 压缩文件 语法
# 压缩以后原文件会消失
gzip 选项 文件目录列表

# 压缩文件testgzip并且打印显示每个文件的名子和压缩率
gzip –v /home/horik/testgzip

# 解压文件
gzip -d testgzip.gz

# 一条命令压缩多个文件，压缩之后，是各自分开的
gzip file1.txt file2.txt

# 压缩过程中，保留原文件
gzip -k file1.txt

# 详细显示压缩的文件的信息，并不解压
gzip -l testgzip.gz
```

---

### 关机和系统管理命令

#### shutdown 系统关机

```shell
# 语法
shutdown(选项)(参数)

# 选项
-c：当执行“shutdown -h 11:50”指令时，只要按+键就可以中断关机的指令；
-f：重新启动时不执行fsck；
-F：重新启动时执行fsck；
-h：将系统关机；
-k：只是送出信息给所有用户，但不会实际关机；
-n：不调用init程序进行关机，而由shutdown自己进行；
-r：shutdown之后重新启动；
-t<秒数>：送出警告信息和删除信息之间要延迟多少秒。

# 参数
[时间]：设置多久时间后执行shutdown指令；
[警告信息]：要传送给所有登入用户的信息。

# 示例
shutdown -h now # 指定现在立即关机
shutdown +5 "System will shutdown after 5 minutes" # 指定5分钟后关机，同时送出警告信息给登入用户
shutdown –h 20:25 # 系统会在今天20:25关机
shutdown –r now # 系统立马重启
shutdown –r +10 # 系统十分钟后重启
```

#### date 显示系统的日期和时间

```shell
horik@Horik:~/playground$ date
2022年 09月 03日 星期六 01:31:23 CST
```

#### who  查看当前在线的用户情况

- 当用户执行 `who am i` 时，只显示运行该命令的用户的信息。

```shell
# 当没有给出非选项参数时，按以下字段顺序为每个当前用户打印信息：登录用户名称，终端信息，登录时间，远程主机或X display。
[root@localhost ~]# who
root     pts/0        2013-08-19 15:04 (192.168.0.134)
root     pts/1        2013-12-20 10:37 (180.111.155.40)

# 列出所有已登录的用户的名称和数量。
[root@localhost ~]# who -q
root root
# users=2

# 打印列标题行
[root@localhost ~]# who -H
NAME     LINE         time             COMMENT
root     pts/0        2013-08-19 15:04 (192.168.0.134)
root     pts/1        2013-12-20 10:37 (180.111.155.40)

[root@localhost ~]# who -w
root     + pts/0        2013-08-19 15:04 (192.168.0.134)
root     + pts/1        2013-12-20 10:37 (180.111.155.40)
```

---

### 用户管理命令

#### useradd 建立用户帐号

```shell
# 建立一个新用户账户，并设置ID：
# 需要说明的是，设定ID值时尽量要大于500，以免冲突。因为Linux安装后会建立一些特殊用户，一般0到499之间的值留给bin、mail这样的系统账号。
useradd horik -u 544

# 新建用户加入组：
useradd –g sales jack –G company,employees    # -g：加入主要组、-G：加入次要组
```

#### groupadd 创建用户组

```shell
groupadd student //创建一个student组
```

#### passwd 修改密码

如果是普通用户执行passwd只能修改自己的密码。如果新建用户后，要为新用户创建密码，则用passwd用户名，注意要以root用户的权限来创建。

普通用户如果想更改自己的密码，直接运行passwd即可

```shell
passwd username	// 设置user的密码
```

#### Linux用户权限说明

```shell
# 查看当前目录（包含隐藏文件）的长格式。
ls -la
  -rw-r--r--   1 user  staff   651 Oct 12 12:53 .gitmodules

# 第1位如果是d则代表目录，是-则代表普通文件。
# 第2到4位代表当前用户的权限。
# 第5到7位代表组用户的权限。
# 第8到10位代表其他用户的权限。
```

#### chmod 变更文件或目录的权限

- 通过符号组合的方式更改目标文件或目录的权限。

	```shell
	chmod [OPTION]... MODE[,MODE]... FILE...
	```

- 通过八进制数的方式更改目标文件或目录的权限。

	```shell
	chmod [OPTION]... OCTAL-MODE FILE...
	```

- 通过参考文件的权限来更改目标文件或目录的权限。

	```shell
	chmod [OPTION]... --reference=RFILE FILE...
	```

> 参考`man chmod`文档的`DESCRIPTION`段落得知：
>
> - `u`符号代表当前用户。
> - `g`符号代表和当前用户在同一个组的用户，以下简称组用户。
> - `o`符号代表其他用户。
> - `a`符号代表所有用户。
> - `r`符号代表读权限以及八进制数`4`。
> - `w`符号代表写权限以及八进制数`2`。
> - `x`符号代表执行权限以及八进制数`1`。
> - `X`符号代表如果目标文件是可执行文件或目录，可给其设置可执行权限。
> - `s`符号代表设置权限suid和sgid，使用权限组合`u+s`设定文件的用户的ID位，`g+s`设置组用户ID位。
> - `t`符号代表只有目录或文件的所有者才可以删除目录下的文件。
> - `+`符号代表添加目标用户相应的权限。
> - `-`符号代表删除目标用户相应的权限。
> - `=`符号代表添加目标用户相应的权限，删除未提到的权限。

示例如下：

```shell
# 添加组用户的写权限。
chmod g+w ./test.log
# 删除其他用户的所有权限。
chmod o= ./test.log
# 使得所有用户都没有写权限。
chmod a-w ./test.log
# 当前用户具有所有权限，组用户有读写权限，其他用户只有读权限。
chmod u=rwx, g=rw, o=r ./test.log
# 等价的八进制数表示：
chmod 764 ./test.log	# 使用此方式设置时，八进制数字的设置对应顺序为ugo，和file的权限信息显示顺序一致
# 将目录以及目录下的文件都设置为所有用户拥有读写权限。
# 注意，使用'-R'选项一定要保留当前用户的执行和读取权限，否则会报错！
chmod -R a=rw ./testdir/
# 根据其他文件的权限设置文件权限。
chmod --reference=./1.log  ./test.log
```

#### chown 变更文件或目录的拥有者或所属群组

**chown命令** 改变某个文件或目录的所有者和所属的组，该命令可以向某个用户授权，使该用户变成指定文件的所有者或者改变文件所属的组。用户可以是用户或者是用户D，用户组可以是组名或组id。文件名可以使由空格分开的文件列表，在文件名中可以包含通配符。

只有文件主和超级用户才可以便用该命令。

```shell
# 将目录`/home/horik`及其下面的所有文件、子目录的文件主改成 horik：
chown -R horik /home/horik
```

---

### 文件阅读命令

#### more 显示文件内容，每次显示一屏

**more命令** 是一个基于vi编辑器文本过滤器，它以全屏幕的方式按页显示文本文件的内容，支持vi中的关键字定位操作。more名单中内置了若干快捷键，常用的有H（获得帮助信息），Enter（向下翻滚一行），空格（向下滚动一屏），Q（退出命令）。

该命令一次显示一屏文本，满屏后停下来，并且在屏幕的底部出现一个提示信息，给出至今己显示的该文件的百分比：--More--（XX%）可以用下列不同的方法对提示做出回答：

- 按 `Space` 键：显示文本的下一屏内容。
- 按 `Enter` 键：只显示文本的下一行内容。
- 按斜线符`/`：接着输入一个模式，可以在文本中寻找下一个相匹配的模式。
- 按``H`键：显示帮助屏，该屏上有相关的帮助信息。
- 按`B`键：显示上一屏内容。
- 按``Q`键：退出more命令。

```shell
# 语法
more arguments... file

# 参数
-<数字>：指定每屏显示的行数；
-d：显示“[press space to continue,'q' to quit.]”和“[Press 'h' for instructions]”；
-c：不进行滚屏操作。每次翻页都刷新屏幕
-s：将多个空行压缩成一行显示；
-u：禁止下划线；
+<数字>：从指定数字的行开始显示。

# 示例
# 显示文件file的内容，但在显示之前先清屏，并且在屏幕的最下方显示完成的百分比。
more -dc file
# 显示文件file的内容，每10行显示一次，而且在显示之前先清屏。
more -c -10 file
```

#### less 分屏上下翻页浏览文件内容

**less命令** 的作用与more十分相似，都可以用来浏览文字档案的内容，不同的是less命令允许用户向前或向后浏览文件，而more命令只能向前浏览。用less命令显示文件时，用PageUp键向上翻页，用PageDown键向下翻页。要退出less程序，应按Q键

```shell
# 语法
less filename

# 示例
less /var/log/shadowsocks.log
```

#### cat ,more, less 命令差异

这三者命令均具有查看文件内容之功能。

- cat命令一次显示所有文件内容，而后两者可与用户交换以方便用户查看。

- cat命令还具有合并文件之功能。
- less允许用户后翻查看已经阅读过的内容；less并未在一开始就读入文件所有内容，因此其在查看文件时比vi速度快。
- less比more支持更多的控制命令

---

### 磁盘管理命令

#### df 显示磁盘的使用

**df命令** 用于显示磁盘分区上的可使用的磁盘空间。默认显示单位为KB。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

**实例**

查看系统磁盘设备，默认是KB为单位：

```shell
horik@Horik:~$ df
文件系统            1K-块      已用      可用 已用% 挂载点
none              3917044         4   3917040    1% /mnt/wsl
drivers         480140612 363464016 116676596   76% /usr/lib/wsl/drivers
none              3917044         0   3917044    0% /usr/lib/wsl/lib
/dev/sdc       1055762868   7060764 994998632    1% /
none              3917044        88   3916956    1% /mnt/wslg
rootfs            3913724      1876   3911848    1% /init
none              3917044         4   3917040    1% /run
none              3917044         0   3917044    0% /run/lock
none              3917044         0   3917044    0% /run/shm
none              3917044         0   3917044    0% /run/user
tmpfs             3917044         0   3917044    0% /sys/fs/cgroup
none              3917044        88   3916956    1% /mnt/wslg/versions.txt
none              3917044        88   3916956    1% /mnt/wslg/doc
drvfs           480140612 363464016 116676596   76% /mnt/c
drvfs            18350076  12716188   5633888   70% /mnt/d
drvfs           488296444  24253600 464042844    5% /mnt/e
drvfs           488447996  93731416 394716580   20% /mnt/f
```

使用`-h`选项以KB以上的单位来显示，可读性高：

```shell
# -h, --human-readable                   以K，M，G为单位，提高信息的可读性。
horik@Horik:~$ df -h
文件系统        容量  已用  可用 已用% 挂载点
none            3.8G  4.0K  3.8G    1% /mnt/wsl
drivers         458G  347G  112G   76% /usr/lib/wsl/drivers
none            3.8G     0  3.8G    0% /usr/lib/wsl/lib
/dev/sdc       1007G  6.8G  949G    1% /
none            3.8G   88K  3.8G    1% /mnt/wslg
rootfs          3.8G  1.9M  3.8G    1% /init
none            3.8G  4.0K  3.8G    1% /run
none            3.8G     0  3.8G    0% /run/lock
none            3.8G     0  3.8G    0% /run/shm
none            3.8G     0  3.8G    0% /run/user
tmpfs           3.8G     0  3.8G    0% /sys/fs/cgroup
none            3.8G   88K  3.8G    1% /mnt/wslg/versions.txt
none            3.8G   88K  3.8G    1% /mnt/wslg/doc
drvfs           458G  347G  112G   76% /mnt/c
drvfs            18G   13G  5.4G   70% /mnt/d
drvfs           466G   24G  443G    5% /mnt/e
drvfs           466G   90G  377G   20% /mnt/f
```

查看全部文件系统：

```shell
horik@Horik:~$ df -ah
文件系统        容量  已用  可用 已用% 挂载点
none            3.8G  4.0K  3.8G    1% /mnt/wsl
drivers         458G  347G  112G   76% /usr/lib/wsl/drivers
none            3.8G     0  3.8G    0% /usr/lib/wsl/lib
/dev/sdc       1007G  6.8G  949G    1% /
none            3.8G   88K  3.8G    1% /mnt/wslg
/dev/sdc       1007G  6.8G  949G    1% /mnt/wslg/distro
rootfs          3.8G  1.9M  3.8G    1% /init
none            3.8G     0  3.8G    0% /dev
sysfs              0     0     0     - /sys
proc               0     0     0     - /proc
devpts             0     0     0     - /dev/pts
none            3.8G  4.0K  3.8G    1% /run
none            3.8G     0  3.8G    0% /run/lock
none            3.8G     0  3.8G    0% /run/shm
none            3.8G     0  3.8G    0% /run/user
binfmt_misc        0     0     0     - /proc/sys/fs/binfmt_misc
tmpfs           3.8G     0  3.8G    0% /sys/fs/cgroup
cgroup2            0     0     0     - /sys/fs/cgroup/unified
cgroup             0     0     0     - /sys/fs/cgroup/cpuset
cgroup             0     0     0     - /sys/fs/cgroup/cpu
cgroup             0     0     0     - /sys/fs/cgroup/cpuacct
cgroup             0     0     0     - /sys/fs/cgroup/blkio
cgroup             0     0     0     - /sys/fs/cgroup/memory
cgroup             0     0     0     - /sys/fs/cgroup/devices
cgroup             0     0     0     - /sys/fs/cgroup/freezer
cgroup             0     0     0     - /sys/fs/cgroup/net_cls
cgroup             0     0     0     - /sys/fs/cgroup/perf_event
cgroup             0     0     0     - /sys/fs/cgroup/net_prio
cgroup             0     0     0     - /sys/fs/cgroup/hugetlb
cgroup             0     0     0     - /sys/fs/cgroup/pids
cgroup             0     0     0     - /sys/fs/cgroup/rdma
cgroup             0     0     0     - /sys/fs/cgroup/misc
none            3.8G   88K  3.8G    1% /mnt/wslg/versions.txt
none            3.8G   88K  3.8G    1% /mnt/wslg/doc
none            3.8G   88K  3.8G    1% /tmp/.X11-unix
drvfs           458G  347G  112G   76% /mnt/c
drvfs            18G   13G  5.4G   70% /mnt/d
drvfs           466G   24G  443G    5% /mnt/e
drvfs           466G   90G  377G   20% /mnt/f
```

显示 `Compiler` 目录中的可用空间量，如以下输出中所示：

```shell
horik@Horik:~$ df Compiler/ -h
文件系统        容量  已用  可用 已用% 挂载点
/dev/sdc       1007G  6.8G  949G    1% /
```

#### du 显示目录的使用情况

**du命令** 也是查看使用空间的，但是与df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的。

**实例**

只显示总和的大小:

```shell
# -s, --summarize                        仅显示总计，只列出最后加总的值。
horik@Horik:~/Compiler/MegaSysy$  du -s .
1239088 .
```

显示总和的大小且易读:

```shell
# -h, --human-readable                   以K，M，G为单位，提高信息的可读性。
horik@Horik:~/Compiler/MegaSysy$ du -sh .
1.2G    .
horik@Horik:~/Compiler/MegaSysy$ du -sh FrontedTestStd/
655M    FrontedTestStd/
horik@Horik:~/Compiler/MegaSysy$ rm -rf FrontedTestStd/
horik@Horik:~/Compiler/MegaSysy$ du -sh .
557M    .
```

文件从大到小排序

```
horik@Horik:~/Compiler/MegaSysy$ du -sh * |sort -rh
655M    FrontedTestStd
490M    FrontedTest
2.0M    lib
1.2M    img
1.1M    src
24K     lib.ll
12K     out.ll
4.0K    test.ll
4.0K    Sysy.g4
```

只显示当前目录下子目录的大小。

```shell
horik@Horik:~/Compiler/MegaSysy$ du -sh ./*/
490M    ./FrontedTest/
1.2M    ./img/
2.0M    ./lib/
1.1M    ./src/
```

查看指定目录下文件所占的空间：

```shell
# 此命令会递归查看指定目录的所有子目录
horik@Horik:~/Compiler/Toolchains$  du ./*
32      ./clang
28      ./llvm
```

---

### 进程管理命令

#### ps 报告当前系统的进程状态

**ps命令** 用于报告当前系统的进程状态。可以搭配kill指令随时中断、删除不必要的程序。ps命令是最基本同时也是非常强大的进程查看命令，使用该命令可以确定有哪些进程正在运行和运行的状态、进程是否结束、进程有没有僵死、哪些进程占用了过多的资源等等，总之大部分信息都是可以通过执行该命令得到的。

```shell
# 语法
ps(选项)
```

由于ps命令能够支持的系统类型相当的多，所以选项多的离谱！限于篇幅，这里就不一一列出了，下面还是写出一些常用实例。（摘自https://wangchujiang.com/linux-command/c/ps.html）

```shell
ps axo pid,comm,pcpu # 查看进程的PID、名称以及CPU 占用率
ps aux | sort -rnk 4 # 按内存资源的使用量对进程进行排序
ps aux | sort -nk 3  # 按 CPU 资源的使用量对进程进行排序
ps -A # 显示所有进程信息
ps -u root # 显示指定用户信息
ps -efL # 查看线程数
ps -e -o "%C : %p :%z : %a"|sort -k5 -nr # 查看进程并按内存使用大小排列
ps -ef # 显示所有进程信息，连同命令行
ps -ef | grep ssh # ps 与grep 常用组合用法，查找特定进程
ps -C nginx # 通过名字或命令搜索进程
ps aux --sort=-pcpu,+pmem # CPU或者内存进行排序,-降序，+升序
ps -f --forest -C nginx # 用树的风格显示进程的层次关系
ps -o pid,uname,comm -C nginx # 显示一个父进程的子进程
ps -e -o pid,uname=USERNAME,pcpu=CPU_USAGE,pmem,comm # 重定义标签
ps -e -o pid,comm,etime # 显示进程运行的时间
ps -aux | grep named # 查看named进程详细信息
ps -o command -p 91730 | sed -n 2p # 通过进程id获取服务名称
```

将目前属于您自己这次登入的 PID 与相关信息列示出来

```shell
ps -l
#  UID   PID  PPID        F CPU PRI NI       SZ    RSS WCHAN     S             ADDR TTY           TIME CMD
#  501   566   559     4006   0  31  0  4317620    228 -      Ss                  0 ttys001    0:00.05 /App...cOS/iTerm2 --server /usr/bin/login -fpl kenny /Ap...s/MacOS/iTerm2 --launch_shel
#  501   592   577     4006   0  31  0  4297048     52 -      S                   0 ttys001    0:00.63 -zsh
```

- F 代表这个程序的旗标 (flag)， 4 代表使用者为 super user
- S 代表这个程序的状态 (STAT)，关于各 STAT 的意义将在内文介绍
- UID 程序被该 UID 所拥有
- PID 就是这个程序的 ID ！
- PPID 则是其上级父程序的ID
- C CPU 使用的资源百分比
- PRI 这个是 Priority (优先执行序) 的缩写，详细后面介绍
- NI 这个是 Nice 值，在下一小节我们会持续介绍
- ADDR 这个是 kernel function，指出该程序在内存的那个部分。如果是个 running的程序，一般就是 "-"
- SZ 使用掉的内存大小
- WCHAN 目前这个程序是否正在运作当中，若为 - 表示正在运作
- TTY 登入者的终端机位置
- TIME 使用掉的 CPU 时间。
- CMD 所下达的指令为何

> 在预设的情况下， `ps` 仅会列出与目前所在的 `bash shell` 有关的 `PID` ，所以， 当前面使用 `ps -l` 的时候，只有三个 PID。

列出目前所有的正在内存当中的程序

```shell
ps aux

# USER               PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
# kenny             6155  21.3  1.7  7969944 284912   ??  S    二03下午 199:14.14 /Appl...OS/WeChat
# kenny              559  20.4  0.8  4963740 138176   ??  S    二03下午  33:28.27 /Appl...S/iTerm2
# _windowserver      187  18.0  0.6  7005748  95884   ??  Ss   二03下午 288:44.97 /Syst...Light.WindowServer -daemon
# kenny             1408  10.7  2.1  5838592 347348   ??  S    二03下午 138:51.63 /Appl...nts/MacOS/Google Chrome
# kenny              327   5.8  0.5  5771984  79452   ??  S    二03下午   2:51.58 /Syst...pp/Contents/MacOS/Finder
```

- USER：该 process 属于那个使用者账号的
- PID ：该 process 的号码
- %CPU：该 process 使用掉的 CPU 资源百分比
- %MEM：该 process 所占用的物理内存百分比
- VSZ ：该 process 使用掉的虚拟内存量 (Kbytes)
- RSS ：该 process 占用的固定的内存量 (Kbytes)
- TTY ：该 process 是在那个终端机上面运作，若与终端机无关，则显示 ?，另外， tty1-tty6 是本机上面的登入者程序，若为 pts/0 等等的，则表示为由网络连接进主机的程序。
- STAT：该程序目前的状态，主要的状态有
	- R ：该程序目前正在运作，或者是可被运作
	- S ：该程序目前正在睡眠当中 (可说是 idle 状态)，但可被某些讯号 (signal) 唤醒。
	- T ：该程序目前正在侦测或者是停止了
	- Z ：该程序应该已经终止，但是其父程序却无法正常的终止他，造成 zombie (疆尸) 程序的状态
- START：该 process 被触发启动的时间
- TIME ：该 process 实际使用 CPU 运作的时间
- COMMAND：该程序的实际指令

找出与 cron 与 syslog 这两个服务有关的 PID 号码

```shell
ps aux | egrep '(cron|syslog)'

# root                50   0.0  0.0  4305532   1284   ??  Ss   二03下午   0:10.08 /usr/sbin/syslogd
# kenny            90167   0.0  0.0  4258468    184 s007  R+    9:23下午   0:00.00 egrep (cron|syslog)
```

把所有进程显示出来，并输出到ps001.txt文件

```shell
ps -aux > ps001.txt
```

输出指定的字段

```shell
ps axo pid,comm,pcpu # 查看进程的PID、名称以及CPU 占用率
```

#### pstree

**pstree命令** 以树状图的方式展现进程之间的派生关系，显示效果比较直观。

```shell
(base) [root@AliyunServer ~]# pstree
systemd─┬─AliSecGuard───6*[{AliSecGuard}]
        ├─AliYunDun───23*[{AliYunDun}]
        ├─AliYunDunUpdate───5*[{AliYunDunUpdate}]
        ├─2*[agetty]
        ├─aliyun-service───7*[{aliyun-service}]
        ├─assist_daemon───7*[{assist_daemon}]
        ├─atd
        ├─auditd───{auditd}
        ├─chronyd
        ├─containerd───8*[{containerd}]
        ├─containerd-shim─┬─java───54*[{java}]
        │                 └─11*[{containerd-shim}]
        ├─crond
        ├─dbus-daemon
        ├─dhclient
        ├─dockerd─┬─2*[docker-proxy───4*[{docker-proxy}]]
        │         └─8*[{dockerd}]
        ├─lvmetad
        ├─master─┬─pickup
        │        └─qmgr
        ├─nginx───nginx
        ├─polkitd───6*[{polkitd}]
        ├─python3───python3───2*[{python3}]
        ├─redis-server───4*[{redis-server}]
        ├─rsyslogd───2*[{rsyslogd}]
        ├─3*[sh───node─┬─node───10*[{node}]]
        │              └─10*[{node}]]
        ├─sshd───sshd───bash───pstree
        ├─systemd-journal
        ├─systemd-logind
        └─systemd-udevd
```

显示当前所有进程的进程号和进程id

```shell
pstree -p
```

显示所有进程的所有详细信息，遇到相同的进程名可以压缩显示。

```shell
pstree  -a
```

获取 SSH 会话的 PID

```shell
pstree -p | grep ssh

#  |-sshd(1221)-+-sshd(2768)---bash(2770)-+-grep(2810)
#  |            `-sshd(2807)---sshd(2808)
```

#### 启动进程

##### 手工启动

###### 前台启动：直接输入命名启动程序

```shell
source ./startDjango.sh
```

###### 后台启动：在命令后输入&操作符

```shell
source ./startDjango.sh &
```

##### 调度启动

###### at 在指定时间执行一个任务

- 在一个指定的时间执行一个指定任务，只能执行一次，且需要开启`atd`进程。

- 可通过命令`ps -ef | grep atd` 搜索进程`atd`是否正在运行。
- 如果没有运行，可通过命令`/etc/init.d/atd start `or `/etc/init.d/atd restart`启动atd进程

**at命令** 用于在指定时间执行命令。at允许使用一套相当复杂的指定时间的方法。它能够接受在当天的hh:mm（小时:分钟）式的时间指定。假如该时间已过去，那么就放在第二天执行。当然也能够使用midnight（深夜），noon（中午），teatime（饮茶时间，一般是下午4点）等比较模糊的 词语来指定时间。用户还能够采用12小时计时制，即在时间后面加上AM（上午）或PM（下午）来说明是上午还是下午。 也能够指定命令执行的具体日期，指定格式为month day（月 日）或mm/dd/yy（月/日/年）或dd.mm.yy（日.月.年）。指定的日期必须跟在指定时间的后面。

上面介绍的都是绝对计时法，其实还能够使用相对计时法，这对于安排不久就要执行的命令是很有好处的。指定格式为：`now + count time-units`，now就是当前时间，time-units是时间单位，这里能够是minutes（分钟）、hours（小时）、days（天）、weeks（星期）。count是时间的数量，究竟是几天，还是几小时，等等。 更有一种计时方法就是直接使用today（今天）、tomorrow（明天）来指定完成命令的时间。

三天后的下午 5 点锺执行`/bin/ls`：

```shell
[root@localhost ~]# at 5pm+3 days
at> /bin/ls
at> <EOT>	# EOT是文本结束符 按ctrl+D就可以
job 7 at 2013-01-08 17:00
```

明天17点钟，输出时间到指定文件内：

```shell
[root@localhost ~]# at 17:20 tomorrow
at> date >/root/2013.log
at> <EOT>
job 8 at 2013-01-06 17:20
```

计划任务设定后，在没有执行之前我们可以用atq命令来查看系统没有执行工作任务：

```shell
    [root@localhost ~]# atq
8       2013-01-06 17:20 a root
7       2013-01-08 17:00 a root
```

删除已经设置的任务：

```shell
[root@localhost ~]# atq
8       2013-01-06 17:20 a root
7       2013-01-08 17:00 a root

[root@localhost ~]# atrm 7
[root@localhost ~]# atq
8       2013-01-06 17:20 a root
```

显示已经设置的任务内容：

```shell
[root@localhost ~]# at -c 8
#!/bin/sh
# atrun uid=0 gid=0
# mail     root 0
umask 22此处省略n个字符
date >/root/2013.log
```

###### crontab 提交和管理用户的需要周期性执行的任务

详情参见https://wangchujiang.com/linux-command/c/crontab.html

**crontab命令** 被用来提交和管理用户的需要周期性执行的任务，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务工具，并且会自动启动crond进程，crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。

它通过/etc/crontab配置文件或者crontab命令实现，这里介绍配置文件编辑方式

`/etc/crontab`文件包括下面几行：

```shell
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=""HOME=/

# run-parts
51 * * * * root run-parts /etc/cron.hourly
24 7 * * * root run-parts /etc/cron.daily
22 4 * * 0 root run-parts /etc/cron.weekly
42 4 1 * * root run-parts /etc/cron.monthly
```

crontab文件的含义：用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：

```shell
minute   hour   day   month   week   command     顺序：分 时 日 月 周
```

**crond服务**

```shell
/sbin/service crond start    # 启动服务
/sbin/service crond stop     # 关闭服务
/sbin/service crond restart  # 重启服务
/sbin/service crond reload   # 重新载入配置
```

查看crontab服务状态：

```shell
service crond status
```

手动启动crontab服务：

```shell
service crond start
```

查看crontab服务是否已设置为开机启动，执行命令：

```shell
ntsysv
```

加入开机自动启动：

```shell
chkconfig –level 35 crond on
```

#### 改变进程的运行方式

##### jobs 显示作业的状态

- 语法 `jobs (args)`

```shell
-l	在作业信息中额外的列出PID。
-n	只列出最近一次通知以来状态变更的作业。
-p	只列出PID。
-r	只输出处于运行状态的作业。
-s	只输出处于停止状态的作业。
```

```shell
[user2@pc] ssh 192.168.1.4
pc@192.168.1.4's password:
# 此时按下ctrl+z使得交互停止。
[1]+  Stopped                 ssh 192.168.1.4

[user2@pc] sleep 60 &
[2] 13338

[user2@pc] jobs
[1]-  Stopped                 ssh 192.168.1.4
[2]   Running                 sleep 60 &

[user2@pc] jobs -l
[1]- 12927 Stopped                 ssh 192.168.1.4
[2]  13338 Running                 sleep 60 &

[user2@pc] jobs -p
12927
13338

[user2@pc] jobs -s
[1]-  Stopped                 ssh 192.168.1.4

[user2@pc] jobs -r
[2]   Running                 sleep 60 &

[user2@pc] kill -9 12927
[2]   Done                    sleep 60

[user2@pc] jobs -n -l
[1]+ 12927 Killed             ssh 192.168.1.4

[user2@pc] jobs -n -l
```

##### bg 把命令程序调到后台

- 用于将作业放到后台运行，使前台可以执行其他任务。该命令的运行效果与在指令后面添加符号`&`的效果是相同的，都是将其放到系统后台执行。
- 若前台任务中只有一个，则使用该命令时可以省略任务号。
- 该命令需要`set`选项`monitor`处于开启状态时才能执行；查看作业控制状态：输入`set -o`查看`monitor`行；执行`set -o monitor`或`set -m`开启该选项。
- 语法：`bg [job_spec ...]`

```shell
# 运行sleep命令，然后按下ctrl+z。
sleep 60
^Z
[1]+  Stopped                 sleep 60

# 使用bg命令使得作业在后台运行。
bg %1

# 返回信息：
[1]+ sleep 60 &
```

##### fg 把后台命令调回前台来

- 用于将后台作业（在后台运行的或者在后台挂起的作业）放到前台终端运行。
- 若后台任务中只有一个，则使用该命令时可以省略任务号。
- 该命令需要`set`选项`monitor`处于开启状态时才能执行；查看作业控制状态：输入`set -o`查看`monitor`行；执行`set -o monitor`或`set -m`开启该选项。
- 语法 `fg [job_spec ...]`

```shell
# 运行sleep命令，然后按下ctrl+z。
sleep 60
^Z
[1]+  Stopped                 sleep 60

# 使用fg命令使得作业在前台运行。
fg %1

# 返回信息：
sleep 60
```

##### kill 发送信号到进程



```shell
# -l        列出信号名称。如果在该选项后提供了数字那么假设它是信号名称对应的数字。
[user2@pc] kill -l 9
KILL

# 列出所有信号名称：
[user2@pc] kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL
 5) SIGTRAP      6) SIGABRT      7) SIGBUS       8) SIGFPE
 9) SIGKILL     10) SIGUSR1     11) SIGSEGV     12) SIGUSR2
13) SIGPIPE     14) SIGALRM     15) SIGTERM     16) SIGSTKFLT
17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU
25) SIGXFSZ     26) SIGVTALRM   27) SIGPROF     28) SIGWINCH
29) SIGIO       30) SIGPWR      31) SIGSYS      34) SIGRTMIN
35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3  38) SIGRTMIN+4
39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12
47) SIGRTMIN+13 48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14
51) SIGRTMAX-13 52) SIGRTMAX-12 53) SIGRTMAX-11 54) SIGRTMAX-10
55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7  58) SIGRTMAX-6
59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX

# 下面是常用的信号。
# 只有第9种信号(SIGKILL)才可以无条件终止进程，其他信号进程都有权利忽略。

HUP     1    终端挂断
INT     2    中断（同 Ctrl + C）
QUIT    3    退出（同 Ctrl + \）
KILL    9    强制终止
TERM   15    终止
CONT   18    继续（与STOP相反，fg/bg命令）
STOP   19    暂停（同 Ctrl + Z）
```

实例

```shell
# 以下发送KILL信号的形式等价。当然还有更多的等价形式，在此不一一列举了。
[user2@pc] kill -s SIGKILL PID
[user2@pc] kill -s KILL PID
[user2@pc] kill -n 9 PID
[user2@pc] kill -9 PID

[user2@pc] sleep 90 &
[1] 178420

# 终止作业标识符为1的作业。
[user2@pc] kill -9 %1

[user2@pc] jobs -l
[1]+ 178420 KILLED                  ssh 192.168.1.4

[user2@pc] sleep 90 &
[1] 181357

# 发送停止信号。
[user2@pc] kill -s STOP 181357

[user2@pc] jobs -l
[1]+ 181537 Stopped (signal)        sleep 90

# 发送继续信号。
[user2@pc] kill -s CONT 181357

[user2@pc] jobs -l
[1]+ 181537 Running                 sleep 90 &
```

---

### 输入输出重定向

#### 标准输入/输出

每个Linux进程都会打开3个文件用来处理程序的输入输出工作

| 文件                       | 文件描述符 | 默认设备 |
| -------------------------- | ---------- | -------- |
| 输入文件：标准输入         | 0          | 键盘     |
| 输出文件：标准输出         | 1          | 屏幕     |
| 错误输出文件：标准错误输出 | 2          | 屏幕     |

#### 重定向

- < 输入重定向

	语法：` 命令 < 输入文件`**

	示例： **`cat < inputfile`**

- \> 输出重定向

	语法：` 命令 > 输出文件`**

	示例：` ls > outputfile`

- 2> 错误输出重定向

	语法： `命令 2>errorfile`

	示例：`ls 2 > inputfile`

**常用文件重定向命令**

| 重定向命令                     | 意义                                       |
| ------------------------------ | ------------------------------------------ |
| command > filename             | 把标准输出重定向到一个文件中               |
| command >> filename            | 把标准输出追加重定向到一个文件中           |
| command 1> fielname            | 把标准输出重定向到一个文件中               |
| command > filename 2 > &1      | 把标准输出和标准错误输出重定向到一个文件中 |
| command 2 > filename           | 把标准错误输出重定向到一个文件中           |
| command < filename > filename2 | 以filename为标准输入，filename2为标准输出  |
| command < filename             | 把filename作为命令的标准输入               |
| command << delimiter           | 从标准输入读入数据，直到遇到delimiter为止  |

---

> **Reference**
>
> [1] 北京航空航天大学软件学院系统编程课程第一章PPT--01 Linux基础
>
> [2] Linux编程基础 李养群等著
>
> [3] [Linux命令搜索引擎](https://wangchujiang.com/linux-command/)

