---
title: 「系统编程」Shell编程FAQ
date: 2022-09-27 16:32:17
thumbnail: https://images.unsplash.com/photo-1629624927838-3b39b7fdd33c?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1169&q=80
categories: 系统编程
tags:
	- Linux基础
    - shell编程
---

> **\<FAQ\>:= \<Basic\> + \<Cooooool>**
>
> 年轻人的自动化脚本启航之旅——shell编程！运行一个脚本，命令行开始跳动不明意义的字符，不觉得这很酷吗？作为一名理工男我觉得这实在是太酷了，很符合我对未来生活的想象，科技并带着趣味...

## 基本语法约定

**bash脚本内容：**

```bash
# 这是一行注释，以#号开头

# 下面这一行不是注释，它的作用是指明脚本使用的解释器，让脚本能跑起来
#!/bin/bash

# 每一行是一个命令
echo "hello"

# 可以用<;> 在同一行使用多个命令
echo "new"; echo "world"

# 逗号连接了一串运算式子，把最后一个表达式的值返回给左值（不觉得拆开写更有可读性吗？奇怪的语法）
let "t2 = ((a = 9, 15 / 3))"
echo t2	# 结果为5

# 转义符号是反斜杠
echo "\""

exit 0	# 正常结束脚本，非0为异常结束
```

**命令行内容：**

```shell
# 运行脚本--方式1
sh <scriptname> # 禁用了脚本stdin中读数据的功能(不推荐)

# 运行脚本--方式2
bash <scriptname>

# 运行脚本--方式3
chmod [555|+rx|u+rx] <scriptname>	# 脚本必须具有可执行权限
./<scriptname>		# 对了,bash脚本以.sh作为后缀名

# 查看脚本的退出码
echo $?	# $?可以读取上一条命令（上一个程序）的退出码
```

## 变量

### 环境变量

环境变量就是工作环境(系统)下的全局变量，列几个我觉得还算有用的

```dart
HOME 用于保持注册目录的完全路径名
PWD 当前工作目录的绝对路径
PS1 主提示符，特权用户是#，普通用户是$。
```

### 用户变量

```shell
# 定义变量
<var_name>=<var_value>	# 这里的等号旁边不能加空格（貌似脚本语言对缩进、空格的要求都挺高的，我猜这是为了方便写解释器）
readonly <var_name>=<var_value> # 只读的变量（感觉没啥用）
# 使用变量
$<var_name>			# 我喜欢这种，写起来简单
${<var_name>}		# 支持数组
"$<var_name>"		# 防止变量中含空格造成错误
"${<var_name>}" 	# 支持数组且支持空格

# 例子
var1=1
var2=${var1}
echo ${var2}
>> 1
echo $var2
>> 1
# 错误例子

var = 1	# 变量赋值不能有空格
>> [ERROR!] var：未找到命令
```


### 奇怪的引号机制

第一次接触shell的时候在引号这里踩了坑，这里记录一下不同引号的作用

**单引号** 

单引号括起来的字符均作为普通字符出现。

```shell
string='$PATH' 
echo $string
>>$PATH # string被视为了纯字符串
```

**双引号**

双引号括起来的字符除$、\\、'、和"之外都将作为普通字符对待。

```shell
string="$PATH"
echo $string
>> /usr/bin:/root	# string内的path被视为了命令
```

**反引号**

后置引用，命令替换。其所括字符串在被Shell解释时，首先执行其中的命令，并将其结果代替该命令

```shell
pwd 
>> /root
string="current directory is `pwd`" 
echo $string
# string中的pwd被命令执行的结构替换了
>> Current directory is /root
```

**引号嵌套**

从最外层开始解释

```shell
#!/bin/bash
B=`cat b.c|grep 234`
echo $B
echo '$B'
>> 1234
>> $B	# 外层的单引号''让变量B没机会替换其定义的内容（个人理解）
```

### 命令行参数

**内部变量**

`$#` `$*`是比较常用的内部变量，可以帮助脚本接受来自命令行的参数信息（args）


```bash
$# 传递给脚本参数的数量
$* 所有传递给脚本的参数内容 
$? 上条命令执行后返回的状态 
$$ 当前进程的进程号→最常见的用途是作为暂存文件的名称，以保证不会重复。
$! 后台运行的最后一个进程号 
$0 当前执行的进程名
$@ 它是$*的另外一种形式，它不使用IFS。
```


IFS是什么？


```shell
$ IFS='' 	# IFS用于设置`$*`多参数之间的分隔符
$ set foo bar bam 
$ echo “$@” 
foo bar bam 
$ echo “$*” 
foobarbam 
$ unset IFS 
$ echo “$*”
foo bar bam
```

**位置参数**

在命令行中按照各自的位置决定的变量，程序名之后输入的参数，之间用空格分割，第 一个参数可使用\$1取得，以此类推，\$0表示当前Shell程序的进程名。**共10个位置参数变量**，从程序名开始依次为 第0~9个

```shell
#!/bin/bash
echo "The name of this script is \"$0\"."
if [ -n "$1" ]; then
    echo "Parameter #1 is $1"
fi
if [ -n "$2" ]; then
    echo "Parameter #2 is $2"
fi
```


```shell
./test.sh 114
The name of this script is "./test.sh".
Parameter #1 is 114
```

怎么使用超过十个位置呢？我们使用**shift**命令，shift 命令重新分配位置参数,其实就是向左移动一个位置

```shell
#!/bin/bash
until [ -z “$1” ]; do # 直到所有参数都用完
    echo -n "$1 "
    shift
done
```

**参数置换变量**

有些参数我们会给定一些默认值，同时也开放接口支持用户在命令行提供参数修改默认值，这时候就要用到参数置换变量的语法了（虽然我感觉用 **if **判断也挺好）。下面这个例子中，如果脚本中没有命令行参数 ,那么default parameter将被使用。

```bash
#！/bin/bash 
DEFAULT_FILENAME=generic.data filename=${1:-$DEFAULT_FILENAME}
echo $filename
```


```shell
./test.sh
>> generic.data
./test.sh abc
>> abc
```

上面的参数置换变量的语法有几种变体，它们的区别在于没有设置参数时的行为，需要的时候自查

## 运算

```bash
#!/bin/bash 
x=0 
while [ "$x" -ne 10 ]; do 
	echo $x 
	x=$(($x+1)) 	# shell中使用$((<exp>))包裹表达式，让解释器知道这里要解释为运算
done
exit 0


# 另外一种方式，使用expr字段，没有括号方便，不推荐
x=$(expr $x +1)
```

## 字符串操作

**字符串长度**

```bash
expr length $stringZ
expr "$stringZ" : '.*'	# 这里其实是子串匹配的特殊情况(正则表达式匹配了所有字符)
${#stringZ}		# #号的意思是集合内元素的个数(在这里就是字符串的长度)

|---例子---|
stringZ=abcABC123ABCabc
${#stringZ}
expr length $stringZ
expr "$stringZ" : '.*'
```

**匹配子串长度**

从字符串开始的位置匹配子串的长度

```bash
expr match "$string" '$substring’	# $substring 是一个正则表达式
expr match "$string" '\($substring\)'
expr "$string" : '$substring’

|---例子---|
echo `expr match "$stringZ" 'abc[A-Z]*.2'` 
>> 8 # abcABC12
echo `expr "$stringZ" : 'abc[A-Z]*.2'`
>> 8 # abcABC12
echo `expr match "$stringZ" 'abc[A-Z]*'` 
>> 6 # abcABC
```

**索引匹配**

匹配到子串的第一个字符出现的位置，**注意**

- 索引从1开始
- 子串是单个字符的集合，索引匹配的机理是：在母串顺序查找字符x，如果x在字串出现过，就停止查找，返回x的索引

```bash
expr index $string $substring

|---例子---|
stringZ=abcABC123ABCabc 
echo `expr index "$stringZ" C12`
>> 6
echo `expr index "$stringZ" 1c` 
>> 3
echo `expr index "$stringZ" 2c` 
>> 3
echo `expr index "$stringZ" b2c` 
>> 2
echo `expr index "$stringZ" a45c`
>> 1
```

**子串提取**

```bash
expr substr $string $position $length
# 在 string 中从位置$position 开始提取子串. 如果$string为“*”或“@”,那么将提取从位置$position 开始的位置参数
${string:position}
# 在 string 中从位置$position 开始提取$length 长度的 子串.
${string:position:length}
# $position可以取负数，将从字符结尾开始,反向提取子串 需要加上圆括号转义
${stringZ:(-4)}
```

**子串删除**

```bash
stringZ=abcABC123ABCabc
[1]
# 从$string 的左边截掉 第一个匹配的 $substring
${string#substring} 
echo ${stringZ#a*C} 
>> 123ABCabc

[2]
# 从$string 的左边截掉最后一个匹配的$substring
${string##substring} 
echo ${stringZ##a*C} 
>> abc


stringZ=abcABC123ABCabc
[3]
# 从$stringZ 的后边开始截掉'b'和'c'之间的最近的匹配
echo ${stringZ%b*c}
>> abcABC123ABCa
[4]
# 从$stringZ 的后边开始截掉'b'和'c'之间的最远的匹配
echo ${stringZ%%b*c}
>> a
```

**子串替换**

```bash
# / 替换第一个匹配的$substring.
${string/substring/replacement}
# // 替换所有匹配的$substring
${string//substring/replacement}

# 如果$substring 匹配$string 的开头部分,那么就用 $replacement 来替换$substring.
${string/#substring/replacement} 
# 如果$substring 匹配$string 的结尾部分,那么就用 $replacement 来替换$substring.
${string/%substring/replacement} 
```



## 条件语句

**条件Condition的写法**

```bash
# 在中括号内写条件语句(推荐)
[condition]
# 在test命令后写条件语句
test condition

# 字符串--条件语句
[str1=str2]
[str1!=str2]
[str]	# true iff str不是空字符
-n str	# true iff len(str) > 0
-z str	# true iff len(str) == 0

# 整数--条件语句
int1 -eq int2	# equal
int1 -ge int2	# greater than
int1 -le int2	# less and equal
int1 -gt int2 	# great than and equal
int1 -ne int2 	# not equal

# 文件--条件语句
-d file		# directory
-f file 	# normal file
-r file		# readable
-s file		# strlen(file)>0
-w file		# writable
-x file 	# executable

# 逻辑符
!expr 	# 取反 
expr1 -a expr2 	# and
expr1 -o expr2	# or
```

**条件控制流的写法**

**if else**

```bash
#!/bin/bash 
if [ $1 -le 10 ];then
	echo “a<=10”
elif [ $1 –le 20 ];then
	echo “10<a<=20”;
else
	echo “a>20”;
fi
```

**case**

```bash
#!/bin/sh 
echo “Is it morning? Please answer yes or no” 
read timeofday 			#等待用户输入 
case "$timeofday" in 
yes | y | Yes | YES） 	# 条件1
	echo "Good Morning" 
	echo “Up bright and early this morning”
[nN]*)					# 条件2
	echo “Good Afternoon” ;;
*)						# 用*通配符来处理无匹配项情况
	echo “Sorry, answer not recognized” echo “Please answer yes or no” exit 1 ;;
esac					# 结束case判断
exit 0
```

**逻辑运算**

支持短路运算

```
||
&&
```

## 循环语句

**while**

```bash
while [condition]; do 
	command(s)
done
```

**Utill**

```bash
until [condition]; do 
	command(s)
done
```

**for**

```bash
for arg in [list]; do 	# list 中的参数允许包含通配符
	command(s)
done
```

## 数组

```bash
area1[13]=1 	# 初始化数组，可以跳着初始化，数组成员不必一定要连贯或连续的，空缺元素是允许的
area2=( zero one two three four ) # 初始化数组，数组下标从零开始
area3=([17]=seventeen [24]=twenty-four)	# 初始化数组，指定位置

echo ${area2[0]}	# 访问数组
```



## 函数

函数必须先定义再调用,调用的时候直接写函数名就可以了

```bash
[function] function_name (){
	commands(s)
} 
```

那传参和返回值怎么办呢?	

- 调用函数的时候在后面加上参数, 使用位置参数 **\$1 \$2** 等进行取参
- 返回值使用 **\$?** 获得

```bash
max 1 2 3
echo $?
```

## 调试

bash没有调试工具，只提供了几个辅助参数

```bash
sh -n scriptname # 不会实际运行脚本,而只是检查脚本的语法错误，该方法不能检测所有的语法错误。
sh -v scriptname # 在实际执行一个命令前打印出这个命令.
sh -x scriptname # 打印每个命令的执行结果, 但只用在某些小的方面.
```

真正调试的时候依赖于printf大法，在各个位置使用**echo**打印信息，高级一点可以在编写程序阶段就使用**assert**

## Trap

在程序退出之前可以加一个hook（钩子函数），执行一系列的动作后再退出程序。原理是exit发出一个信号signal，在我们设置特定的trap后，这种信号会触发trap（类似中断），执行trap的处理函数。这常用来捕捉exit 命令做某事, 如强制打印变量值

```bash
# trap 命令必须是脚本中的第一个命令。
#!/bin/sh 
trap ‘rm -f /tmp/my_tmp_file_$$’ INT 
echo creating file /tmp/my_tmp_file_$$ 
date > /tmp/my_tmp_file_$$ 
echo “press interrupt (CTRL-C) to interrupt ....” 
while [ -f /tmp/my_tmp_file_$$ ]; do 
	echo File exists sleep 1 
done
echo The file no longer exists
```

信号量一览

| Signal | Description            |
| ------ | ---------------------- |
| HUP    | 挂起                   |
| INT    | Ctrl+C引发的中断       |
| QUIT   | Ctrl+\引发的退出       |
| ABRT   | 严重执行错误引发的中止 |
| ALRM   | 定时处理的报警信号     |
| TERM   | 终止，系统关机时发送   |

---

好啦，你已经学会shell编程辣！！！~~现在快用shell手搓一个快排吧~~

> FLAG：感觉比较重量级的bash脚本是用来跑文件批处理的（自动化测试|批量运行|批量重命名），python也能干这活儿，不知道有什么优劣区别，有机会实践一下再POST一篇新内容吧

