

# shell脚本入门

学习视频：

https://www.bilibili.com/video/BV14L4y157Bv/?p=2&spm_id_from=pageDriver&vd_source=a2b5d34472e8f7063a990de528e8c560



## 一、shell基础

### 1.1 什么是shell

- 解释执行用户输入的命令或程序等
- 用户输入一条命令，shell就解释一条
- 键盘输入命令，Linux给与响应的方式，称之为交互式

shell是一块包裹着系统核心的壳，处于操作系统的最外层，与用户直接对话，把用户的输入，解释给操作系统，然后处理操作系统的输出结果，输出到屏幕给用户看到结果。

<img src="./shell%E8%84%9A%E6%9C%AC%E5%85%A5%E9%97%A8.assets/image-20240625093801046.png" alt="image-20240625093801046" style="zoom: 67%;" />



> Shell 也可以被视为一种编程语言。更具体地说，Shell 是一种特定于命令行界面的脚本语言，用于与操作系统交互、自动化常见任务、管理系统文件和程序。Shell 语言提供了一个功能强大的编程环境，允许你编写脚本以执行批处理操作、调用操作系统服务、处理文本文件等。



### 1.2 什么是shell脚本

当命令或者程序语句写在文件中，我们执行文件，读取其中的代码，这个程序文件就称为shell脚本。

在shell脚本里定义多条Linux命令以及循环控制语句，然后将这些Linux命令一次性执行完毕，执行脚本文件的方式称之为非交互式方式

```shell
windows中存在“.bat”批处理脚本
Linux中常用“.sh”脚本文件
```



### 1.3  Shebang

计算机程序中，==shebang指的是出现在文本文件中的第一行前两个字符 `#!`==

在Unix系统中，程序会分析shebang后面的内容，作为解释器的指令，例如

- 以`#!/bin/sh`开头的文件，程序在执行的时候会调用/bin/sh，也就是bash解释器
- 以`#!/usr/bin/python`开头的文件，代表指定Python解释器去执行
- 以`#!/usr/bin/env`解释器名称，是一种在不同平台上都能正确找到解释器的办法

> 如果脚本未指定shebang，脚本执行的时候，默认从当前shell去解释脚本，即`$SHELL`

```shell
xiaoxue@xiaoxue-virtual-machine:~$ echo $SHELL
/bin/bash

```

```shell
# 没有权限
xiaoxue@xiaoxue-virtual-machine:~$ ./hello.py
bash: ./hello.py: Permission denied

# 增加权限
chmod +x hello.py  
```



### 1.4 执行shell脚本的方式

- `bash script.sh`或`sh scripte.sh`，文件本身没权限执行，没x权限，则使用的方法，或脚本未指`Shebang`重点推荐的方式
- 使用`绝对/相对`路径执行脚本，需要文件含有x权限
- `source script.sh`或者`. script.sh`，代表`执行`的含义，



### 1.5  shell和运维

shell脚本语言很适合处理纯文本类型数据，如日志、配置文件、文本、网页文件、

shell是==一种弱类型语言==，在定义数据类型的时候，不用主动声明该类型，

ubuntu支持的shell

```C++
xiaoxue@xiaoxue-virtual-machine:~$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
```



### 1.6  bash基本特性

- bash是一个命令处理器，运行在文本窗口中，并能执行用户直接输入的命令
- bash还能从文件中读取Linux命令，称之为脚本
- bash支持通配符、管道、命令替换、条件判断等逻辑控制语句



bash将之前的命令都进行了存储，通过`history`，命令可以查看，此外，这些命令也是保存在文件中的

```shell
xiaoxue@xiaoxue-virtual-machine:~$ vim ~/.bash_history 
```

 在 Linux 中，`echo` 命令是一个常用的 shell 命令，用于在终端显示一行文本或者字符串。这个命令非常实用，可以用来显示变量的内容、生成需要的输出以及在脚本中进行调试。

```shell
xiaoxue@xiaoxue-virtual-machine:~$ echo $HISTSIZE
1000

# 存放用户执行的历史命令，写入文件
xiaoxue@xiaoxue-virtual-machine:~$ echo $HISTFILE
/home/xiaoxue/.bash_history
```

`history`命令

```shell
-c 清空历史
-r 恢复历史
    
xiaoxue@xiaoxue-virtual-machine:~$ history -c
xiaoxue@xiaoxue-virtual-machine:~$ history 
    1  history 

xiaoxue@xiaoxue-virtual-machine:~$ history -r ~/.bash_history 
xiaoxue@xiaoxue-virtual-machine:~$ history
    1  history 
    2  history -r ~/.bash_history 
    3  sudo apt-get install vim
    4  sudo apt-get install git
    5  sudo apt-get install g++
    6  wget https://osm-download.etsi.org/ftp/osm-13.0-thirteen/install_osm.sh
    7  chmod +x install_osm.sh
    8  ./install_osm.sh 2>&1 | tee osm_install_log.txt
    9  kubectl get all -n osm
   10  python
   11  python3
   12  sudo apt-get install geany
   13  123
   14  sudo apt-get install geany
   15  history

# 调用历史记录命令：
`！历史id`，快速执行历史命令
`!!`，执行上次的命令
```

bash特性汇总

```shell
文件路径tab键补全
命令补全
快捷键ctrl + a, e, u, k, l
通配符：正则表达式
命令历史：history
命令别名
命令行展开
```



### 1.7  shell变量

- Shell编程中变量分为三种，分别是系统变量、环境变量和用户变量；
- **Shell变量名在定义时，首个字符必须为字母（a-z，A-Z），不能以数字开头，中间不能有空格，可以使用下划线（_），不能使用（-），也不能使用标点符号等。**

简单的变量介绍：

```shell
root@xiaoxue-virtual-machine:~# age=18
root@xiaoxue-virtual-machine:~# echo $age
18
```

==单引号不能识别特殊语法，双引号可以识别特殊语法==

```shell
xiaoxue@xiaoxue-virtual-machine:~$ name="zhaoxiaoxue"
xiaoxue@xiaoxue-virtual-machine:~$ name1='${name}'
xiaoxue@xiaoxue-virtual-machine:~$ echo $name1
${name}
xiaoxue@xiaoxue-virtual-machine:~$ name2="${name}"
xiaoxue@xiaoxue-virtual-machine:~$ echo $name2
zhaoxiaoxue
xiaoxue@xiaoxue-virtual-machine:~$ 
```



- 本地变量：只针对当前的shell进程：`pstree`检查进程树

- 环境变量：全局变量

- 局部变量：针对在shell函数或者是shell脚本中定义

- 位置参数变量：用于在shell脚本中传递的参数

- 特殊变量：shell内置的特殊功效变量

  - $？

  - 0：成功

  - 1-255：错误码

    ```shell
    xiaoxue@xiaoxue-virtual-machine:~$ ls /opt
    containerd
    
    # 查询上条命令是否执行成功
    xiaoxue@xiaoxue-virtual-machine:~$ echo $?
    0
    ```

    

- 自定义变量

  - 变量赋值：varName=value
  - 变量引用：`${varName}`、   `$varName`



1. ==每次调用`bash/sh`解释器执行脚本，都会开启一个`子shell`因此不保留当前的`shell`变量，通过`pstree`命令检查进程树==
2. ==调用`source`或者`.`符号，在当前shell环境加载脚本，因此保留变量==

```shell
#这个反引号``作用
xiaoxue@xiaoxue-virtual-machine:~$ ls
Desktop    Downloads  install_osm.sh  OSM-ETSI-Release-key.gpg  Pictures  snap       Videos
Documents  hello.py   Music           osm_install_log.txt       Public    Templates
xiaoxue@xiaoxue-virtual-machine:~$ name=`ls`
xiaoxue@xiaoxue-virtual-machine:~$ echo $name
Desktop Documents Downloads hello.py install_osm.sh Music OSM-ETSI-Release-key.gpg osm_install_log.txt Pictures Public snap Templates Videos
```



#### 1 环境变量设置

环境变量，能找出你的Linux服务器上，各种变量。环境变量一般指的是用`export`内置命令导出的变量，用于定义shell的运行环境，保证shell命令的正确执行。

shell通过环境变量确定登录的用户名、`PATH`路径、文件系统等各种应用

环境变量可以在命令行中临时创建，如要永久生效，需要修改**环境变量配置文件**：

- 用户个人配置文件`~/.bash_profile`，`~/.bashrc`远程登录用户特有文件
- 全局配置文件`/etc/profile`、`/etc/bashrc`且系统建议最好创建在`/etc/profile.d/`，而非直接修改主文件，修改全局配置文件，影响所有登录系统的用户

```shell
# 这个命令用于列出你的家目录（由~表示）中的所有文件和目录，包括那些以点（.）开头的隐藏文件
xiaoxue@xiaoxue-virtual-machine:~$ ls ~ -a
.              .cache     .gnupg          Music                     Public                     .thunderbird
..             .config    hello.py        OSM-ETSI-Release-key.gpg  .python_history            Videos
.bash_history  Desktop    install_osm.sh  osm_install_log.txt       snap                       .viminfo
.bash_logout   Documents  .local          Pictures                  .sudo_as_admin_successful
.bashrc        Downloads  .mozilla        .profile   
```

自己的ubuntu系统`.profile`文件是在登录shell中执行的配置文件，用于设置用户的环境变量和初始环境

```shell
xiaoxue@xiaoxue-virtual-machine:~$ cat ~/.profile
# ~/.profile: executed by the command interpreter for login shells. 
# This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login 
# exists.
# see /usr/share/doc/bash/examples/startup-files for examples.
# the files are located in the bash-doc package.

# the default umask is set in /etc/profile; for setting the umask
# for ssh logins, install and configure the libpam-umask package.
#umask 022

# if running bash
if [ -n "$BASH_VERSION" ]; then  #  $BASH_VERSION环境变量非空
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
	. "$HOME/.bashrc"
    fi
fi

# set PATH so it includes user's private bin if it exists 
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
xiaoxue@xiaoxue-virtual-machine:~$ 

```

自己的CentOS系统是`.bash_profile`

```shell
[jianzi@master ~]$ ls ~ -a
.              .bash_logout   .config    .local          ??????  ??????
..             .bash_profile  .dbus      .mozilla        ??????  ??????
.ICEauthority  .bashrc        .esd_auth  .mysql_history  ??????  ??????
.bash_history  .cache         .lesshst   software        ??????  ??????
[jianzi@master ~]$ 
```



- 检查系统环境变量的命令

```shell
set,输出所有变量，包括全局变量、局部变量
env,只显示全局变量
declare,输出所有的变量，如同set
export,显示和设置环境变量值
```

```shell
xiaoxue@xiaoxue-virtual-machine:~$ export |awk -F '[ :=]' '{print $3}'
COLORTERM
DISPLAY
HOME
LANG
LC_ADDRESS
LC_IDENTIFICATION
LC_MEASUREMENT
LC_MONETARY
LC_NAME
LC_NUMERIC
LC_PAPER
LC_TELEPHONE
LC_TIME
LESSCLOSE
LESSOPEN
LOGNAME
LS_COLORS
MAIL
OLDPWD
PATH
PWD
SHELL
SHLVL
SUDO_COMMAND
SUDO_GID
SUDO_UID
SUDO_USER
TERM
USER
XAUTHORITY
xiaoxue@xiaoxue-virtual-machine:~$ 

```



- 撤销环境变量

unset变量名，删除变量或函数

- 设置只读变量

readonly，只有shell结束，只读变量失效

```shell
xiaoxue@xiaoxue-virtual-machine:~$ readonly name="123"
xiaoxue@xiaoxue-virtual-machine:~$ name="456"
bash: name: readonly variable
```





#### 2 特殊参数变量

- `$0`获取shell脚本文件名，以及脚本路径
- `$n`获取shell脚本的第n个参数，n在1~9之间，如`$1,$2,$9`大于9则需要写，`${10}`参数空格隔开
- `$#`获取执行的shell脚本后面的参数总个数
- `$*`获取shell脚本所有参数，不加引号等同于`$@`作用，叫上引号`"$*"`作用是接受所有参数为单个字符串
- `$@`不加引号，效果同上，加上引号，是接受所有参数为**独立字符串**

```shell
xiaoxue@xiaoxue-virtual-machine:~$ cat special_var.sh 
#! /bin/bash
echo '特殊变量 $0 $1 $2 ... 实践'
echo '结果: ' $0 $1 $2

echo '#########################'
echo '特殊变量$# 获取参数总个数'
echo '结果: ' $#

echo '########################'
echo '特殊变量$* 实践'
echo '结果: ' $*

echo '########################'
echo '特殊变量$@ 实践'
echo '结果: ' $@

xiaoxue@xiaoxue-virtual-machine:~$ bash special_var.sh yu chao 180 180 180 180
特殊变量 $0 $1 $2 ... 实践
结果:  special_var.sh yu chao
#########################
特殊变量$# 获取参数总个数
结果:  6
########################
特殊变量$* 实践
结果:  yu chao 180 180 180 180
########################
特殊变量$@ 实践
结果:  yu chao 180 180 180 180

```

>面试题：

![image-20240626115914222](./shell%E8%84%9A%E6%9C%AC%E5%85%A5%E9%97%A8.assets/image-20240626115914222.png)

```shell
xiaoxue@xiaoxue-virtual-machine:~$ cat different.sh 
#! /bin/bash
echo "print each param from \"\$*\""
for var in "$*"
do
	echo "$var"
done

echo "print each param from \"\$@\""
for var in "$@"
do
	echo "$var"
done

xiaoxue@xiaoxue-virtual-machine:~$ bash different.sh yu chao 180 180
print each param from "$*"
yu chao 180 180
print each param from "$@"
yu
chao
180
180

```

#### 3 特殊状态变量

- `$?`上一次命令执行状态返回值，`0`正确，非0失败

- `$$`当前shell脚本的进程号

- `$!`上一次后台进程的PID

- `$_`在此之间执行的命令，最后一个参数

- 查找方式  man bash

  - 搜索Special Parameters

  

> 脚本控制返回值的玩法，脚本返回值，学习shell函数编程之后，才能彻底理解。这个脚本执行完毕了，会返回一个数字id，称之为返回值

```bash
xiaoxue@xiaoxue-virtual-machine:~$ cat t1.sh
#! /bin/bash
# $#获取参数个数，  -ne不等于的情况
[ $# -ne 2 ] &&{
	echo "must be two args"
          exit 119 #终止程序运行，且返回119状态码，提供给当前shell的$?变量，若是在函数里 可以 return 119用法

}
echo "没毛病，就是2个参数"
xiaoxue@xiaoxue-virtual-machine:~$ bash t1.sh yu chao 180
must be two args
xiaoxue@xiaoxue-virtual-machine:~$ echo $?
119
xiaoxue@xiaoxue-virtual-machine:~$ bash t1.sh yu chao 
没毛病，就是2个参数
xiaoxue@xiaoxue-virtual-machine:~$ echo $?
0
xiaoxue@xiaoxue-virtual-machine:~$ 
```

> 获取上一次后台执行的程序PID， `$! `获取

```bash
#怎么让程序后台执行
nohup xxx & 1 > /dev/null

nohup: 这是一个命令行实用程序，用于运行另一个命令，同时忽略所有挂起（SIGHUP）信号。这通常用于在退出登录或关闭终端时继续运行程序。
xxx: 想要在后台执行的命令或脚本。
&: 将命令放在后台运行。
1> /dev/null: 重定向标准输出（文件描述符 1）到 /dev/null，
```

```bash

xiaoxue@xiaoxue-virtual-machine:~$ nohup ping baidu.com & 1> /dev/null
[1] 3713
xiaoxue@xiaoxue-virtual-machine:~$ nohup: ignoring input and appending output to 'nohup.out'

xiaoxue@xiaoxue-virtual-machine:~$ 
xiaoxue@xiaoxue-virtual-machine:~$ ps -ef|grep ping
xiaoxue     2102    1754  0 14:26 ?        00:00:00 /usr/libexec/gsd-housekeeping
xiaoxue     3706    3460  0 16:16 pts/2    00:00:00 ping baidu.com
xiaoxue     3713    2294  0 16:18 pts/0    00:00:00 ping baidu.com
xiaoxue     3715    2294  0 16:18 pts/0    00:00:00 grep --color=auto ping
xiaoxue@xiaoxue-virtual-machine:~$ echo $!
3713
```

> 获取当前脚本的PID

```bash
xiaoxue@xiaoxue-virtual-machine:~$ cat t1.sh
#! /bin/bash
# $#获取参数个数，  -ne不等于的情况
[ $# -ne 2 ] &&{
	echo "must be two args"
          exit 119 #终止程序运行，且返回119状态码，提供给当前shell的$?变量，若是在函数里 可以 return 119用法

}
echo "没毛病，就是2个参数"
echo "当前的脚本id是：$$"

xiaoxue@xiaoxue-virtual-machine:~$ bash t1.sh yu chao
没毛病，就是2个参数
当前的脚本id是：3752
xiaoxue@xiaoxue-virtual-machine:~$ bash t1.sh yu chao
没毛病，就是2个参数
当前的脚本id是：3753
```

> `$_`获取上次命令的最后一个参数

```bash
xiaoxue@xiaoxue-virtual-machine:~$ bash t1.sh yu chao
没毛病，就是2个参数
当前的脚本id是：3753
xiaoxue@xiaoxue-virtual-machine:~$ echo $_
chao
```





### 1.8 shell子串

#### 1 bash一些基础的内置命令

echo

```bash
-n 不换行输出
-e 解析字符串中的特殊符号 
\n 换行
\r 回车
\t 制表符 四个空格
\b 退格
```

```bash
# -n
xiaoxue@xiaoxue-virtual-machine:~$ echo 123; echo 456
123
456
xiaoxue@xiaoxue-virtual-machine:~$ echo -n 123; echo 456
123456
xiaoxue@xiaoxue-virtual-machine:~$ echo -n 123; echo -n 456
123456xiaoxue@xiaoxue-virtual-machine:~$ 

# -e
xiaoxue@xiaoxue-virtual-machine:~$ echo "hello\nworld"
hello\nworld
xiaoxue@xiaoxue-virtual-machine:~$ echo -e "hello\nworld"
hello
world

```

printf

```bash
# printf打印命令
xiaoxue@xiaoxue-virtual-machine:~$ printf "hello\tworld\tzxx\n"
hello	world	zxx
```

eval

```bash
# eval执行多条命令
xiaoxue@xiaoxue-virtual-machine:~$ eval ls;cd /tmp
Desktop       hello.py        OSM-ETSI-Release-key.gpg  snap            Videos
different.sh  install_osm.sh  osm_install_log.txt       special_var.sh
Documents     Music           Pictures                  t1.sh
Downloads     nohup.out       Public                    Templates
xiaoxue@xiaoxue-virtual-machine:/tmp$ 
```

exec

```bash
# exec不创建子进程，执行后续命令，且执行完毕后，自动exit

```

#### 2 shell子串的花式用法

```bash
# 从开头删除匹配最短
## 从开头删除匹配最长
% 从结尾删除匹配最短
%% 从结尾删除匹配最长

a*c 匹配开头为a,中间任意个字符，结尾为c的字符串
a*c 匹配开头为a,中间任意个字符，结尾为c的字符串


${变量}                 返回变量值
${#变量}                返回变量长度，字符长度        
${变量:start}           返回变量Offset数值之后的字符
${变量:start:length}    提取offset之后的length限制的字符
${变量#word}			 从变量开头删除最短匹配的word子串	
${变量##word}			 从变量开头删除最长匹配的word
${变量%word}			 从变量结尾删除最短的word
${变量%%word}			 从变量结尾删除最长匹配的word


${变量/pattern/string}  用string代替第一个匹配的pattern
${变量//pattern/string} 用string代替所有的pattern
```

**字符串的截取**

```bash
# 字符串的截取
xiaoxue@xiaoxue-virtual-machine:~$ name="I am chaoge"
xiaoxue@xiaoxue-virtual-machine:~$ echo $name
I am chaoge
xiaoxue@xiaoxue-virtual-machine:~$ echo ${name:2:5}
am ch

xiaoxue@xiaoxue-virtual-machine:~$ name2="abcABC123ABCabc"
xiaoxue@xiaoxue-virtual-machine:~$ echo ${name2#a*c}
ABC123ABCabc
xiaoxue@xiaoxue-virtual-machine:~$ echo ${name2##a*c}
xiaoxue@xiaoxue-virtual-machine:~$ echo ${name2%a*c}
abcABC123ABC
xiaoxue@xiaoxue-virtual-machine:~$ echo ${name2%%a*c}


xiaoxue@xiaoxue-virtual-machine:~$ str1="Hello,man,i am your brother"
xiaoxue@xiaoxue-virtual-machine:~$ echo ${str1/man/girl}
Hello,girl,i am your brother
xiaoxue@xiaoxue-virtual-machine:~$ echo ${str1//o/O}
HellO,man,i am yOur brOther
```

多种统计长度的命令

```bash
# 方法一
xiaoxue@xiaoxue-virtual-machine:~$ echo $name | wc -L    # 最长的一行多少个字符
9
xiaoxue@xiaoxue-virtual-machine:~$ echo $name | wc -l   # 多少行
1

# 方法二
xiaoxue@xiaoxue-virtual-machine:~$ expr length "${name}"
9

# 方法三
xiaoxue@xiaoxue-virtual-machine:~$ echo "${name}" | awk '{print length($0)}'
9

# 方法四：最快的统计方法
xiaoxue@xiaoxue-virtual-machine:~$ echo ${#name}
9
```

统计命令执行速度

```bash
xiaoxue@xiaoxue-virtual-machine:~$ time for n in {1..10000}; do char=`seq -s "chage" 100`;echo ${#char} &>/dev/null;done

real	0m10,843s  # 实际运行的时间
user	0m6,849s  # 用户态执行的时间
sys	    0m3,792s  # 内核态执行的时间


xiaoxue@xiaoxue-virtual-machine:~$ time for n in {1..10000}; do char=`seq -s "chage" 100`;echo ${char} |wc -L  &>/dev/null;done

real	1m5,063s
user	0m46,419s
sys	    0m39,781s

xiaoxue@xiaoxue-virtual-machine:~$ time for n in {1..10000}; do char=`seq -s "chage" 100`;expr length "${char}"  &>/dev/null;done

real	0m19,688s
user	0m12,558s
sys	0m6,718s
```

> 总结，shell编程，尽量使用Linux内置的命令，内置的操作，和内置的函数，效率最高C语言开发，效率最高，尽可能的减少管道符的操作

删除文件名

```bash
xiaoxue@xiaoxue-virtual-machine:~$ touch chaochao_{1..5}_finished.jpg
xiaoxue@xiaoxue-virtual-machine:~$ touch chaochao_{1..5}_finished.png
xiaoxue@xiaoxue-virtual-machine:~$ ls -l
total 1256
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_1_finished.jpg
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_1_finished.png
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_2_finished.jpg
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_2_finished.png
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_3_finished.jpg
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_3_finished.png
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_4_finished.jpg
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_4_finished.png
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_5_finished.jpg
-rw-rw-r-- 1 xiaoxue xiaoxue       0 6-р сар  26 20:37 chaochao_5_finished.png
# 去掉所有文件的——finished字符信息去掉

# 更改一个文件
xiaoxue@xiaoxue-virtual-machine:~$ f=chaochao_1_finished.jpg
xiaoxue@xiaoxue-virtual-machine:~$ mv $f `echo ${f//_finished/}`

# 更改剩余的.jpg文件
xiaoxue@xiaoxue-virtual-machine:~$ echo `ls *fin*.jpg`
chaochao_2_finished.jpg chaochao_3_finished.jpg chaochao_4_finished.jpg chaochao_5_finished.jpg

xiaoxue@xiaoxue-virtual-machine:~$ for file_name in `ls *fin*.jpg`;do echo $file_name;done
chaochao_2_finished.jpg
chaochao_3_finished.jpg
chaochao_4_finished.jpg
chaochao_5_finished.jpg
xiaoxue@xiaoxue-virtual-machine:~$ for file_name in `ls *fin*.jpg`;do mv $file_name `echo ${file_name//_finished/}`;done
xiaoxue@xiaoxue-virtual-machine:~$ echo `ls *.jpg`
chaochao_1.jpg chaochao_2.jpg chaochao_3.jpg chaochao_4.jpg chaochao_5.jpg
xiaoxue@xiaoxue-virtual-machine:~$ 


```

#### 3  特殊shell扩展变量

```perl
如果parameter变量值为空，返回word字符串
result=${parameter:-word}

如果parameter变量值为空，word字符串替换变量值，且返回其值
result=${parameter:=word}

如果parameter变量值为空，word当做stderr报错信息输出，否则输出变量值
用于设置变量为空导致错误时，返回的错误信息
${parameter:?word}

如果parameter变量值为空，什么都不做，否则word返回
${parameter:+word}
```

> :-判断变量如果值为空，就返回后面的字符信息，可以通过result变量去接收

```perl
# 变量为空
[root@master ~]# echo $chage

[root@master ~]# result=${chage:-heiheihei}
[root@master ~]# echo $result
heiheihei
[root@master ~]# echo $chage

[root@master ~]#

# 变量不为空
[root@master ~]# chage="180"
[root@master ~]# result2=${chage:-heiheihei}
[root@master ~]# echo $result2
180

```

> :=如果变量为空，后面的值赋值给接收者已经变量本身，如果变量不为空，不赋值

```perl
[root@master ~]# unset chaoge
[root@master ~]# unset result
[root@master ~]# result=${chaoge:=apple}
[root@master ~]# echo $result; echo $chaoge
apple
apple
[root@master ~]# res=${chaoge:=xigua}
[root@master ~]# echo $chaoge; echo $res
apple
apple

```

> :?变量为空返回错误信息

```perl
[root@master ~]# echo ${new_name}

[root@master ~]# echo ${new_name:?}
-bash: new_name: 参数为空或未设置
[root@master ~]# echo ${new_name:?该变量值为空}
-bash: new_name: 该变量值为空
[root@master ~]# new_name="chaodi"
[root@master ~]# echo ${new_name:?该变量值为空}
chaodi

```

> :+，如果变量为空，什么都不做，否则返回给接收者

```bash
[root@master ~]# unset chaog res result
[root@master ~]# echo $result

[root@master ~]# echo ${result:+}

[root@master ~]# result=123
[root@master ~]# echo ${result:+你好}
你好

[root@master ~]# res=${result:+你好}
[root@master ~]# echo $res
你好

```

实际应用：数据备份，删除过期数据的脚本

```bash
find xargs 搜索，且删除
find 需要搜索的目录  -name 你要搜索的文件名字 -type 文件类型  -mtime +7 |xargs rm -f

# 扩展变量用法
find ${dir_path:=/data/mysql_back_data/} -name '*.tar.gz' -type f -mtime +7|xargs rm -f

```

### 1.9 父子shell

![image-20240628112819836](./shell%E8%84%9A%E6%9C%AC%E5%85%A5%E9%97%A8.assets/image-20240628112819836.png)

> 1. source和点，执行脚本，只在当前的shell环境中执行生效
> 2. 执行bash sh解释器运行脚本，是开启subshell，开启子shell运行脚本命令
> 3. `./script`，都会指定shebang，通过解释器运行，也是开启subshell运行命令

#### 父shell

> 通过pstree看到如下结果，就是父shell环境

```perl
# pstree
        ├─sshd─┬─sshd───bash───pstree
        │      └─sshd───sftp-server

```



```perl
# ps -ef --forest
root      20683    958  0 08:53 ?        00:00:00  \_ sshd: root@pts/2
root      20712  20683  0 08:53 pts/2    00:00:00  |   \_ -bash
root      22601  20712  0 11:44 pts/2    00:00:00  |       \_ ps -ef --forest
root      20688    958  0 08:53 ?        00:00:00  \_ sshd: root@notty
root      20704  20688  0 08:53 ?        00:00:00      \_ /usr/libexec/openssh/sftp-server

```

![image-20240628114928719](./shell%E8%84%9A%E6%9C%AC%E5%85%A5%E9%97%A8.assets/image-20240628114928719.png)

> 每输入一次`bash`就是创建一个子shell，输入`exit`退出子shell

![image-20240628115128374](./shell%E8%84%9A%E6%9C%AC%E5%85%A5%E9%97%A8.assets/image-20240628115128374.png)

#### 创建进程列表（创建子shell）

> 利用()开启子shell
>
> shell的进程列表理念，需要使用()小括号，如下执行方式，就称之为，进程列表

```perl
(cd ~;pwd;ls;cd /tmp/; pwd;ls;echo $BASH_SUBSHELL)
```

```perl
# linux默认的有关shell的变量
# 该变量的值特定：如果是0就是在当前shell环境中执行的，否则就是开辟子shell去运行的
BASH_SUBSHELL
```

> 检测是否开启了子shell运行命令

```perl
[root@master ~]# cd ~;pwd;ls;echo $BASH_SUBSHELL
/root
anaconda-ks.cfg  initial-setup-ks.cfg  公共  模板  视频  图片  文档  下载  音乐  桌面
0

```

> 明确开启子shell运行的命令，进程列表，并且开启子shell运行命令

```perl
[root@master ~]# (cd ~;pwd;ls;echo $BASH_SUBSHELL)
/root
anaconda-ks.cfg  initial-setup-ks.cfg  公共  模板  视频  图片  文档  下载  音乐  桌面
1
[root@master ~]#
```

> 子shell嵌套运行

```perl
[root@master ~]# (cd ~;pwd;ls;(echo $BASH_SUBSHELL))
/root
anaconda-ks.cfg  initial-setup-ks.cfg  公共  模板  视频  图片  文档  下载  音乐  桌面
2

[root@master ~]# (cd ~;pwd;ls;(pwd;(echo $BASH_SUBSHELL)))
/root
anaconda-ks.cfg  initial-setup-ks.cfg  公共  模板  视频  图片  文档  下载  音乐  桌面
/root
3

```

#### 内置命令、外置命令

> 内置命令：在系统启动的时候就加载入内存，常驻内存，执行效率高，但是占用资源
>
> 外置命令：用户需要从硬盘中读取程序文件，在读入内存加载
>
> 通过type命令，验证是否是内置、外置命令
>
> 外置命令一定会开启子shell进行执行
>
> 内置命令不会产生子进程去执行，内置命令和shell是一体的，是shell的一部分，不需要单独去读取某个文件，系统启动后，就执行在内存中了

```perl
[root@master ~]# type ps
ps 是 /usr/bin/ps
[root@master ~]# ls -l /usr/bin/ps
-rwxr-xr-x. 1 root root 100112 10月  1 2020 /usr/bin/ps
[root@master ~]#

[root@master ~]# type cd
cd 是 shell 内嵌

```

```perl
# 找到Linux所有内嵌指令
[root@master ~]# compgen -b

```

### 1.10 vim复制粘贴命令

```perl
d  删除
y  复制 (默认是复制到"寄存器")
p  粘贴 (默认从"寄存器"取出内容粘贴)
+y 复制到系统剪贴板(也就是vim的+寄存器) 
+p 从系统剪贴板粘贴 
u  撤销
v  从光标当前位置开始，光标所经过的地方会被选中，再按一下v结束。
V  从光标当前行开始，光标经过的行都会被选中，再按一下Ｖ结束。 
ggVG 文章全选（gg-到首行，V-选择行，G-到尾行）


Esc->gg->v->GG->y->p

```



## 二、shell脚本开发

> shebang

```perl
#! /bin/bash
#! /bin/perl
#! /bin/python
```

> 执行脚本的方式

```perl
source my_first.py
. my_first.py
bash my_first.py
./my_first.py
```

> echo，在Linux下的格式化打印

```perl

[root@master ~]# cat echo_test.sh
#! /bin/bash

# 这是用于显示日期和谁登录的脚本
echo "The time and date are: "
date
echo ""
echo "Let's us see who's logged into the system: "
who
[root@master ~]# sh echo_test.sh
The time and date are:
2024年 06月 28日 星期五 17:53:32 CST

Let's us see who's logged into the system:
jianzi   :0           2024-05-15 21:50 (:0)
jianzi   pts/0        2024-05-15 21:50 (:0)
jianzi   pts/1        2024-05-15 21:51 (:0)
root     pts/2        2024-06-28 17:32 (192.168.74.1)
[root@master ~]#

```

```perl
[root@master ~]# cat echo_var.sh
#! /bin/bash
# 显示你的用户个人信息
echo "User into for username: ${USER}"
echo "User UID is: $UID"
echo "User Home is: " $HOME

[root@master ~]# sh echo_var.sh
User into for username: root
User UID is: 0
User Home is:  /root
[root@master ~]#

```

> 变量在脚本中的使用，变量被引用的时候，会赋予其值，脚本中的变量，在shell执行完毕后，当用不同的执行脚本的方式，产生的后果也不一样：
>
> - source和.是在当前的shell环境中加载变量，执行脚本
> - bash或sh去执行脚本的时候，是开启子shell运行的，变量也是在子shell环境中加载，子shell退出后，变量也消失了

```perl
```

> Linux,shell变量的替换引用

```perl
#特殊符号
${vars} 取出变量结果
``
$() 在括号中执行命令，且返回执行结果
() 开启子shell执行命令
```

```perl
[root@master ~]# echo "当前的时间是$(date)"
当前的时间是2024年 06月 28日 星期五 18:10:37 CST
[root@master ~]#

[root@master ~]# echo "当前的用户是`whoami`"
当前的用户是root
[root@master ~]#

```

### 2.1 shell数值计算

#### (())

> (())这个shell扩展计算的语法，在括号里面支持变量的定义，赋值操作

```perl
[root@master ~]# echo $((6 < 7&& 3 < 4))
1
[root@master ~]# echo $((2+1))
3
[root@master ~]#

[root@master ~]# num=5
[root@master ~]# ((num=num*3))
[root@master ~]# echo $num
15

[root@master ~]# b=$((2+2**3))
[root@master ~]# echo $b
10


```

==脚本案例==

```perl
[root@master ~]# cat test.sh
#! /bin/bash

print_usage(){
        printf "Please enter an integer\n"
        exit 1
}

# read -p “提示信息”  接受用户输入的变量
read -p "Please input your number: "   firstnum


# 进行对用户输入判断 if语句
# 语法：中括号前后必须一个空格
# sed把数字替换成空

if [ -n "`echo $firstnum|sed 's/[0-9]//g'`"  ]
  then print_usage
fi

read -p "Please input your operator: "  operator

if [ "${operator}" != "+" ] && [ "${operator}" != "-" ]  && [ "${operator}" != "*" ] && [ "${operator}" != "/"  ]
   then
        echo "只允许输入+-*/"
        exit 2
fi


read -p "Please input your second number: "  secondnum

if [ -n "`echo $secondnum|sed 's/[0-9]//g'`" ]
        then
                print_usage
fi

echo "${firstnum}${operator}${secondnum}结果是：$((${firstnum}${operator}${secondnum}))"


```

#### let命令

> let命令的执行，效果等同于双小括号，但是双小括号效率更高

```perl
[root@master ~]# num=8
[root@master ~]# let num=num+4
[root@master ~]# echo $num
12
```

==脚本案例==

```perl
xiaoxue@xiaoxue-virtual-machine:~$ cat check_nginx_status.sh
#! /bin/bash

CheckUrl(){

        timeout=5

        # 相当于定义一个计数器
        fails=0
        success=0

        while true
        do
                wget --timeout=${timeout} --tries=1 http://pythonav.cv/ -q -0 /dev/null

                # if的条件参数，-ne是不等于的意思
                if [  $? -ne 0 ]
                then
                        let fails=fails+1
                else
                        let success=success+1
                fi

                # -ge 大于等于的意思
                if [ $success -ge 1 ]
                then
                        echo "恭喜你，该网站健康的在运行"
                        # 返回一个状态码
                        exit 0
                fi

                if [ $fails -ge 2 ]; then
                        echo "该网站一定是挂了，或者有问题了，超哥快去检查！"
                        exit 2
                fi
        done


}



# 注意函数定义后，一定要调用，执行
CheckUrl
xiaoxue@xiaoxue-virtual-machine:~$

```

#### expr命令

> 简单的计算器执行命令，但是不好用，用空格来区分

```perl

xiaoxue@xiaoxue-virtual-machine:~$ expr 5 + 3
8

# expr不能识别*，需要转译
xiaoxue@xiaoxue-virtual-machine:~$ expr 5 * 3
expr: syntax error: unexpected argument ‘chaochao_1.jpg’

xiaoxue@xiaoxue-virtual-machine:~$ expr 5 \* 3
15

# expr length 用法
xiaoxue@xiaoxue-virtual-machine:~$ expr length 1234567
7

# expr模式匹配
xiaoxue@xiaoxue-virtual-machine:~$ expr ycc.png ":" ".*p"
5
xiaoxue@xiaoxue-virtual-machine:~$ expr yc.jpg ":" ".*\.jpg"
6
xiaoxue@xiaoxue-virtual-machine:~$ expr yc.jpggggg ":" ".*\.jpg"
6
```

==脚本案例1==

> expr命令判断文件名后缀是否合法

```perl

xiaoxue@xiaoxue-virtual-machine:~$ cat file_houzhui.sh
#! /bin/bash


if expr "$1" ":" ".*\.jpg"  > /dev/null

then
        echo "这的确是以jpg结尾的文件~恭喜你"
else
        echo "这不是jpg文件！不好意思"
fi


xiaoxue@xiaoxue-virtual-machine:~$ bash file_houzhui.sh zxx.jpg
这的确是以jpg结尾的文件~恭喜你
xiaoxue@xiaoxue-virtual-machine:~$ bash file_houzhui.sh zyr.png
这不是jpg文件！不好意思

```

==脚本案例2==

```perl

xiaoxue@xiaoxue-virtual-machine:~$ cat length_word.sh
#! /bin/bash



# 利用for循环
for str1 in I am Yu chao, I teach you to learn linux.
do
        # -lt 表示less then小于
        if [ `expr length $str1` -lt 5  ]
           then
                echo $str1
        fi
done
xiaoxue@xiaoxue-virtual-machine:~$ bash length_word.sh
I
am
Yu
I
you
to

```

#### bc计算器

> bc命令结合管道符

```perl
xiaoxue@xiaoxue-virtual-machine:~$ echo "4.2*4" | bc
16.8

xiaoxue@xiaoxue-virtual-machine:~$ num=5
xiaoxue@xiaoxue-virtual-machine:~$ result=`echo $num*4 | bc`
xiaoxue@xiaoxue-virtual-machine:~$ echo $result
20

```

> 计算1~100的和

```perl
# 方案1
xiaoxue@xiaoxue-virtual-machine:~$ result=`echo $num*4 | bc`
xiaoxue@xiaoxue-virtual-machine:~$ echo $result
20
xiaoxue@xiaoxue-virtual-machine:~$ echo {1..100}
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34 35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52 53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70 71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88 89 90 91 92 93 94 95 96 97 98 99 100
xiaoxue@xiaoxue-virtual-machine:~$ echo {1..100} | tr " " "+"
1+2+3+4+5+6+7+8+9+10+11+12+13+14+15+16+17+18+19+20+21+22+23+24+25+26+27+28+29+30+31+32+33+34+35+36+37+38+39+40+41+42+43+44+45+46+47+48+49+50+51+52+53+54+55+56+57+58+59+60+61+62+63+64+65+66+67+68+69+70+71+72+73+74+75+76+77+78+79+80+81+82+83+84+85+86+87+88+89+90+91+92+93+94+95+96+97+98+99+100
xiaoxue@xiaoxue-virtual-machine:~$ result=`echo {1..100} | tr " " "+" | bc`
xiaoxue@xiaoxue-virtual-machine:~$ echo $result
5050
xiaoxue@xiaoxue-virtual-machine:~$

# 方案2

xiaoxue@xiaoxue-virtual-machine:~$ seq -s "+" 100
1+2+3+4+5+6+7+8+9+10+11+12+13+14+15+16+17+18+19+20+21+22+23+24+25+26+27+28+29+30+31+32+33+34+35+36+37+38+39+40+41+42+43+44+45+46+47+48+49+50+51+52+53+54+55+56+57+58+59+60+61+62+63+64+65+66+67+68+69+70+71+72+73+74+75+76+77+78+79+80+81+82+83+84+85+86+87+88+89+90+91+92+93+94+95+96+97+98+99+100
xiaoxue@xiaoxue-virtual-machine:~$ echo $((`seq -s "+" 100`))
5050


# 方案3

xiaoxue@xiaoxue-virtual-machine:~$ seq -s " + " 100
1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 + 10 + 11 + 12 + 13 + 14 + 15 + 16 + 17 + 18 + 19 + 20 + 21 + 22 + 23 + 24 + 25 + 26 + 27 + 28 + 29 + 30 + 31 + 32 + 33 + 34 + 35 + 36 + 37 + 38 + 39 + 40 + 41 + 42 + 43 + 44 + 45 + 46 + 47 + 48 + 49 + 50 + 51 + 52 + 53 + 54 + 55 + 56 + 57 + 58 + 59 + 60 + 61 + 62 + 63 + 64 + 65 + 66 + 67 + 68 + 69 + 70 + 71 + 72 + 73 + 74 + 75 + 76 + 77 + 78 + 79 + 80 + 81 + 82 + 83 + 84 + 85 + 86 + 87 + 88 + 89 + 90 + 91 + 92 + 93 + 94 + 95 + 96 + 97 + 98 + 99 + 100
xiaoxue@xiaoxue-virtual-machine:~$ seq -s " + " 100 | xargs expr
5050

```

#### awk支持数值计算

```perl

xiaoxue@xiaoxue-virtual-machine:~$ echo "3.2 2.2" | awk '{print $1+$2}'
5
xiaoxue@xiaoxue-virtual-machine:~$ echo "3.2 2.2" | awk '{print ($1*$2)}'
6

# gawk支持浮点型运算
xiaoxue@xiaoxue-virtual-machine:~$ echo "3.2 2.2" | gawk '{print ($1*$2)}'
7.04

```

#### 中括号运算

> 不怎么用，都用(())

```perl
xiaoxue@xiaoxue-virtual-machine:~$ num=5
xiaoxue@xiaoxue-virtual-machine:~$ echo $[num+5]
10

```

### 2.2 shell条件测试

|   条件测试语法   |                 说明                 |
| :--------------: | :----------------------------------: |
| test<测试表达式> | test和<测试表达式>之间至少有一个空格 |
|  [<测试表达式>]  |   [  ]的边界和内容之间至少一个空格   |
| [[<测试表达式>]] |  [[  ]]的边界和内容之间至少一个空格  |
| ((<测试表达式>)) |          (())两端不需要空格          |

#### test条件测试

> test命令评估一个表达式，它的结果是真还是假，如果条件为真，那么命令执行状态码结果为0，否则就不为0,通过$？取值
>
> -e判断该文件是否存在，（普通文件，目录），存在就为真

==参数大全==

```perl
语法
关于某个文件名的「类型」侦测(存在与否)，如test -e filename

针对文件类型判断真假
-e该文件名是否存在? (常用)
-f该文件名是否存在且为普通文本文件(file)? (常用) 
-d该文件名是否为目录(directory)? (常用)
-b该文件名是否为一个block device 裝置?
-c该文件名是否为一个character device 装置?
-S该文件名是否为一个Socket 文件?
-P该文件名是否为一个FIFO (pipe) 文件?
-L该文件名是否为一个连结档?


2.关于文件的权限侦测，如test -r filename
-r 侦测该文件名是否具有可读的属性?
-W侦测该文件名是否具有可写的属性?
-x侦测该文件名是否具有可执行的属性?
-u侦测该文件名是否具有SUID的属性?
-g侦测该文件名是否具有SGID的属性?
-k侦测该文件名是否具有Sticky bit的属性?
-s侦测该文件名是否为非空白文件?

3.两个文件之间的比较，如: test file1 -nt file2

4.关于两个整数之间的判定，例如test n1 -eq n2
#针对变量数值的大小比较判断
-eq 两数值相等( equal)
-ne 两数值不等(not equal)
-gt n1大于n2 (greater than)
-lt n1小于n2 (less than)
 ge n1大于等于n2 (greater than or equal )
-le n1小于等于n2 (less than or equal)

5. 判定宇符串的数据
test -z string 判定字符串是否为0?若string为空字符串，则为true 
test -n string 判定字符串是否非为0 ?若string为空字符串，则为false。
注:
-n亦可省略
test str1 = str2 判定str1是否等于str2 ，若相等，则回传true
test str1 != str2 判定str1是否不等于str2，若相等，则回传false

6.多重条件判定，例如: test -r filename -a -x filename
-a (and)两状况同时成立!例如test -r file -a -x file,则file 同时具有r与x权限时，才回传true。
-o (or)两状况任何一个成立!例如test -r file -0 -X file,则file具有r或x权限时，就可回传true 。
！反相状态，如test ! -x file,当file不具有x时，回传true

```



```perl
(base) xiaoxue@xiaoxue-virtual-machine:~$ echo $?
0
(base) xiaoxue@xiaoxue-virtual-machine:~$ test -e hello.pyy
(base) xiaoxue@xiaoxue-virtual-machine:~$ echo $?
1

```

==[ ]括号条件测试，用的多==

```perl
xiaoxue@xiaoxue-virtual-machine:~$ chmod 0 大碗宽面.txt  # 先删除权限
xiaoxue@xiaoxue-virtual-machine:~$ cat 大碗宽面.txt
cat: 大碗宽面.txt: Permission denied
xiaoxue@xiaoxue-virtual-machine:~$

xiaoxue@xiaoxue-virtual-machine:~$ cat 大碗宽面.txt
cat: 大碗宽面.txt: Permission denied
xiaoxue@xiaoxue-virtual-machine:~$ [ -r "大碗宽面.txt" ] && cat 大碗宽面.txt || echo "你没有权限"
你没有权限
xiaoxue@xiaoxue-virtual-machine:~$ echo 123 > 大碗宽面.txt
-bash: 大碗宽面.txt: Permission denied
xiaoxue@xiaoxue-virtual-machine:~$ [ -w "大碗宽面.txt" ] && (echo "你看看灯，它又大又亮" > 大碗宽面.txt) || echo "你没有写入权限"
你没有写入权限
xiaoxue@xiaoxue-virtual-machine:~$ chmod +w 大碗宽面.txt
xiaoxue@xiaoxue-virtual-machine:~$ chmod +r 大碗宽面.tx
xiaoxue@xiaoxue-virtual-machine:~$ [ -r "大碗宽面.txt" ] && cat 大碗宽面.txt || echo "你没有权限"
你看看灯，它又大又亮


xiaoxue@xiaoxue-virtual-machine:~$ [[ -w "大碗宽面.txt" ]] && (echo "你看看灯，它又大又亮" >> 大碗宽面.txt) || echo "你没有写入权限"
xiaoxue@xiaoxue-virtual-machine:~$ [[ -w "大碗宽面.txt" ]] && (echo "你看看灯，它又大又亮" >> 大碗宽面.txt) || echo "你没有写入权限"
xiaoxue@xiaoxue-virtual-machine:~$ [[ -w "大碗宽面.txt" ]] && (echo "你看看灯，它又大又亮" >> 大碗宽面.txt) || echo "你没有写入权限"
xiaoxue@xiaoxue-virtual-machine:~$ [ -r "大碗宽面.txt" ] && cat 大碗宽面.txt || echo "你没有权限"
你看看灯，它又大又亮
你看看灯，它又大又亮
你看看灯，它又大又亮
你看看灯，它又大又亮

```

==多看大神写的脚本，比如参考系统自带的脚本==

```shell
xiaoxue@xiaoxue-virtual-machine:~$ ls /etc/init.d
acpid       avahi-daemon      cups-browsed  hwclock.sh         mysql            plymouth                     rsync              ssh                  whoopsie
alsa-utils  bluetooth         dbus          irqbalance         network-manager  plymouth-log                 rsyslog            udev                 x11-common
anacron     console-setup.sh  docker        kerneloops         nginx            pppd-dns                     saned              ufw
apparmor    cron              gdm3          keyboard-setup.sh  open-vm-tools    procps                       speech-dispatcher  unattended-upgrades
apport      cups              grub-common   kmod               openvpn          pulseaudio-enable-autospawn  spice-vdagent      uuidd
xiaoxue@xiaoxue-virtual-machine:~$

```



#### 字符串比较测试

|              |                                                      |
| :----------: | :--------------------------------------------------: |
|  -n string   | 判定字符串是否非为0 ?若string为空字符串，则为false。 |
|  -z string   |    判定字符串是否为0?若string为空字符串，则为true    |
| str1 = str2  |      判定str1 是否等于str2 ，若相等，则回传true      |
| str1 != str2 |     判定str1 是否不等于str2，若相等，则回传false     |

> 一定要给字符串添加双引号
>
> = 左右两边得有空格

#### 数值比较测试

|      |       |                 说明                  |
| :--: | :---: | :-----------------------------------: |
| -eq  | ==或= |          两数值相等( equal)           |
| -ne  |  ！=  |         两数值不等(not equal)         |
| -gt  |   >   |        n1大于n2 (greater than)        |
| -lt  |   <   |         n1小于n2 (less than)          |
| -ge  |  >=   | n1大于等于n2 (greater than or equal ) |
| -le  |  <=   |   n1小于等于n2 (less than or equal)   |

> 在单中括号中，使用数学比较符合，请添加转义符号
>
> 在双括号中，不需要使用转义符号了

```perl

xiaoxue@xiaoxue-virtual-machine:~$ [ 1 > 2  ] && echo ok || echo no
ok
xiaoxue@xiaoxue-virtual-machine:~$ [ 1 \> 2  ] && echo ok || echo no
no

```

#### 逻辑判断符号

|      |      |    说明    |
| :--: | :--: | :--------: |
|  -a  |  &&  |  同时为真  |
|  -o  | \|\| | 有一个为真 |
|  !   |  !   |     非     |



```perl
xiaoxue@xiaoxue-virtual-machine:~$ file1=/etc/intit.d/nectwork
xiaoxue@xiaoxue-virtual-machine:~$ file2=/etc/hostname
xiaoxue@xiaoxue-virtual-machine:~$ echo $file1 $file2
/etc/intit.d/nectwork /etc/hostname
xiaoxue@xiaoxue-virtual-machine:~$ [ -f "$file1" -a -f "$file2" ] && echo yes || echo no
no
xiaoxue@xiaoxue-virtual-machine:~$ [ -f "$file1" -o -f "$file2" ] && echo yes || echo no
yes


xiaoxue@xiaoxue-virtual-machine:~$ a=""
xiaoxue@xiaoxue-virtual-machine:~$ b="yuyu"
xiaoxue@xiaoxue-virtual-machine:~$ test -n "$a" && echo yes || echo no
no
xiaoxue@xiaoxue-virtual-machine:~$ [[ -n "$a" && "$a" = "$b" ]] &&  echo yes || echo no
no
xiaoxue@xiaoxue-virtual-machine:~$ a="zxx"
xiaoxue@xiaoxue-virtual-machine:~$ [[ -n "$a" && ! "$a" = "$b" ]] &&  echo yes || echo no
yes
xiaoxue@xiaoxue-virtual-machine:~$ [[ -n "$a" && "$a" != "$b" ]] &&  echo yes || echo no
yes

```

==脚本案例==

```perl
xiaoxue@xiaoxue-virtual-machine:~$ cat t3.sh
#!/bin/bash

read -p "pls input a char : " var1

# 逻辑条件测试

[ "$var1" -eq "1" ] && {
        echo $var1
        exit 0

}


[ "$var1" = "2" ] && {
        echo $var1
        exit 0
}


# 只能输入的是1或是2，否则就报错
[ "$var1" != "2" -a "$var1" != "1" ] && {
        echo "脚本出错，必须输入1或2"
        exit 1
}
```



### 2.3 安装lnmp/lamp脚本开发

```shell
#! /bin/bash

# 判断脚本目录是否存在
path=/home/xiaoxue/shell_program/test_scripts/


# 条件判断
# 开发脚本，真和假2个情况，优先处理错误的逻辑情况，因为错误的情况最容易处理

[ ! -d "$path" ] && mkdir -p $path

# 开发该脚本的正常逻辑
cat <<END
        1.[install lamp]
        2.[intstall lnmp]
        3.[exit]
        pls input the num you want:
END


read num


# 根据该num变量进行逻辑处理
expr $num + 1 &> /dev/null


# 判断上条命令的结果
[ $? -ne 0 ] && {

        echo "The num you input must be {1|2|3}"
        exit 1
}


# 对输入的数字，是1、2、3判断
[ "$num" -eq "1" ] && {
        echo "Starting installing lamp......waiting..."
        sleep 2;

        # 执行lamp.sh安装脚本
        # 对文件权限判断
        [ -x "$path/lamp.sh" ] || {
                echo "The file does not exist or can't be exec."
                exit 1
        }

        $path/lamp.sh
        exit $?

}



[ "$num" -eq "2" ] && {
        echo "Starting installing lnmp......waiting...."
        sleep 2

        [ -x "$path/lnmp.sh" ] || {
                echo "The file does not exist or can't be exec.0"
                exit 1
        }
        $path/lnmp.sh
        exit $?
}


[ "$num" -eq 3 ] && {
        echo "byebye."
        exit 3
}

# 限制用户必须输入的是1,2,3
# [[]] 支持正则表达式 [[ $num =~ [1-3]  ]]

[[ !  "$num" =~ [1-3] ]] && {
        echo "The num you input must be {1|2|3}"
        echo "你这个大笨蛋"
        exit 4
}

```

> 这个脚本有些问题，但是不知道是什么



```shell

xiaoxue@xiaoxue-virtual-machine:~$ bash lamp_or_lnmp.sh
        1.[install lamp]
        2.[intstall lnmp]
        3.[exit]
        pls input the num you want:
2
Starting installing lnmp......waiting....
The file does not exist or can't be exec.0

xiaoxue@xiaoxue-virtual-machine:~$ bash lamp_or_lnmp.sh
        1.[install lamp]
        2.[intstall lnmp]
        3.[exit]
        pls input the num you want:
1
Starting installing lamp......waiting...
The file does not exist or can't be exec.

xiaoxue@xiaoxue-virtual-machine:~$ bash lamp_or_lnmp.sh
        1.[install lamp]
        2.[intstall lnmp]
        3.[exit]
        pls input the num you want:
3
byebye.

xiaoxue@xiaoxue-virtual-machine:~$ bash lamp_or_lnmp.sh
        1.[install lamp]
        2.[intstall lnmp]
        3.[exit]
        pls input the num you want:
4
The num you input must be {1|2|3}
你这个大笨蛋
xiaoxue@xiaoxue-virtual-machine:~$

```

### 2.4 if语句开发

```shell
#if语法

# 方式一
if  <条件表达式>
	then
		代码。。。。。
fi

# 方式二
if  <条件表达式>;then
	代码。。。。。
fi

# if语句嵌套
if  <条件表达式>
	then
		代码1。。。。。
		if <条件表达式>
			then
				代码2。。。。。
		fi
fi


# if-else
if <条件表达式>
	then 
		当条件成立，会执行我。。。
else
	否则会执行我。。。
fi	


#多分支处理
if <条件表达式1>
	then 
		代码1
elif <条件表达式2>
	then 
		代码2
elif <条件表达式3>
	then 
		代码3
else
	否则会执行我。。。
fi	
```

#### 脚本案例1

```shell
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ cat if_1.sh
#! /bin/bash

if [ -f /etc/hosts ]
        then
                echo "[ ] it's ok"
fi



if [[ -f /etc/hosts ]]; then
        echo "[[ ]] it's ok"
fi


if test -f /etc/hosts ; then
        echo "test it's ok"
fi
```

```sh
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ bash if_1.sh
[ ] it's ok
[[ ]] it's ok
test it's ok
```



#### 脚本案例2-开发系统监控脚本

> 开发she11脚本
> 1.检测Linux剩余可用内存，当可用内存小于100M，就发邮件给运维
> 2.并且该脚本加入crontab，没三分钟检查一次内存

1.获取当前内存情况
2.配置邮件告警，用linux发送邮件 (mai1服务的配置)，邮件内容是内存剩余情况
3.开发脚本，判断剩余内存是否小于100M, 1f判断
4.脚本加入crontab,写规则

```sh
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ cat free_1.sh
#! /bin/bash

FreeMem=`free -m | awk 'NR==2 {print $NF}'`
CHARS="Current memeory is $FreeMem"


if [ "$FreeMem" -lt "6000" ]
        then
                echo $CHARS|tee /tmp/messages.txt
                # mail -s "`date +%F-%T $CHARS`" zhaoxiaoxue1123@163.com < /tmp/message.txt
                echo "内存不足，抓紧维护服务器"
fi
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ bash free_1.sh
Current memeory is 5007
内存不足，抓紧维护服务器

```

#### 脚本案例3

```sh
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ cat if_read.sh
#! /bin/bash

a=$1
b=$2

if [ "$a" -lt "$b" ]
        then
                echo "yes, $a less than $b"
                exit 0
fi


if [ "$a" -eq "$b" ]
        then
                echo "yes, $a equal $b"
                exit 1
fi

if [ "$a" -gt "$b" ]
        then
               echo "yes, $a gather than $b"
               exit 2
fi
```



```sh
xiaoxue@xiaoxue-virtual-machine:~/shell_program/scripts$ cat if2_read.sh
#! /bin/bash

a=$1
b=$2


if [ "$a" -lt "$b" ];then
        echo "yes, $a less than $b"
elif [ "$a" -eq "$b" ];then
        echo "yes, $a equal $b"
else
        echo "no~, $a gather than $b"
fi
```



#### ==脚本案例4-开发mysql监控脚本==

|                      |                                                              |
| :------------------: | ------------------------------------------------------------ |
|       端口监控       | 1）在服务器本地监控服务端口的常见命令有netstat、ss、 lsof<br/>2）从远端监控服务器本地端口的命令有telnet、nmap、nc |
| 监控服务进程或进程数 | 此方法适合本地服务器，注意，过滤的是进程的名字。命令为:<br/>ps -ef\|grep nginx\| wc -l<br/>ps -ef\|grep mysql\|wc -l |
| 在客户端模拟用户访问 | 使用wget或curl命令进行测试(如果监测数据库，则需要转为通过Web服<br/>务器去访问数据库)，并对测试结果做三种判断:<br/>1）利用返回值`echo $?`判断<br/>2）获取特殊字符串以进行判断（需要事先开发好程序）<br/>3）根据HTTP响应header的情况进行判断 |
| 登录MySQL数据库判断  | 通过MySQL客户端连接数据库，根据返回值或返回内容判断<br/>例如：`mysql -uroot -p ` |

> 服务器本地端口监控，MySQL状态

```perl
# 方式一
root@xiaoxue-virtual-machine:/home/xiaoxue# netstat -tunlp |grep mysql
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      230766/mysqld
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      230766/mysqld
root@xiaoxue-virtual-machine:/home/xiaoxue# netstat -tunlp |grep mysql | wc -l
2

# 方式二
root@xiaoxue-virtual-machine:/home/xiaoxue# ss -tunlp|grep mysql
tcp     LISTEN   0        151            127.0.0.1:3306           0.0.0.0:*      users:(("mysqld",pid=230766,fd=23))
tcp     LISTEN   0        70             127.0.0.1:33060          0.0.0.0:*      users:(("mysqld",pid=230766,fd=21))
root@xiaoxue-virtual-machine:/home/xiaoxue# ss -tunlp|grep mysql | wc -l
2

# 方式三
root@xiaoxue-virtual-machine:/home/xiaoxue# lsof -i tcp:3306
COMMAND    PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  230766 mysql   23u  IPv4 850217      0t0  TCP localhost:mysql (LISTEN)

```

> 远程监控mysql

```perl
# 方式一
root@xiaoxue-virtual-machine:/home/xiaoxue# nmap 127.0.0.01 -p 3306
Starting Nmap 7.80 ( https://nmap.org ) at 2024-07-03 11:45 +08
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000087s latency).

PORT     STATE SERVICE
3306/tcp open  mysql

Nmap done: 1 IP address (1 host up) scanned in 0.09 seconds

root@xiaoxue-virtual-machine:/home/xiaoxue# nmap 127.0.0.01 -p 3306 | grep 3306
3306/tcp open  mysql


# 方式二
root@xiaoxue-virtual-machine:/home/xiaoxue# echo - e "\n" |telnet 172.0.0.1 3306
Trying 172.0.0.1...
telnet: Unable to connect to remote host: Connection refused
root@xiaoxue-virtual-machine:/home/xiaoxue# echo - e "\n" |telnet 127.0.0.1 3306
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
Connection closed by foreign host.

root@xiaoxue-virtual-machine:/home/xiaoxue# echo - e "\n" |telnet 127.0.0.1 3306 2>/dev/null |grep Connected
Connected to 127.0.0.1.

```

> 进程检查

```perl
# ps 命令用于显示当前系统中正在运行的进程。
# -e 选项显示所有进程。
# -f 选项以完整格式显示进程信息，包括 UID、PID、PPID、C、STIME、TTY、TIME 和 CMD 等列

root@xiaoxue-virtual-machine:/home/xiaoxue# ps -ef |grep mysql
mysql     230766       1  0 7-р сар02 ? 00:14:12 /usr/sbin/mysqld
root      239056  238271  0 11:53 pts/1    00:00:00 grep --color=auto mysql

root@xiaoxue-virtual-machine:/home/xiaoxue# ps -ef |grep mysql |grep -v grep
mysql     230766       1  0 7-р сар02 ? 00:14:12 /usr/sbin/mysqld

```

> 通过访问应用程序接口，读取数据库，查看是否能拿到数据，从而得知mysql是否运行
>
> php程序
>
> Python程序



> php连接mysql，php一种流行的开源服务器端脚本语言

```php
```



> python程序连接mysql

```sh
#终端运行命令，安装pymysql库
sudo apt-get update
sudo apt-get install python3-pip
pip3 install pymysql

```

```python
root@xiaoxue-virtual-machine:/home/xiaoxue# cat test_python_mysql.py
import pymysql

# 建立数据库连接
db = pymysql.connect(
    host="localhost",
    user='root',
    password='123456',
    db='mysql',
    charset='utf-8'
)

cursor=db.cursor()  # 创建游标对象
cursor.execute('select version()')  # 执行一个 SQL 查询，获取 MySQL 数据库的版本信息
data=cursor.fetchone()  # 从查询结果中获取一行数据
print("数据库连接正确，该数据库版本是：%s" %data)
db.close()

```

```shell
root@xiaoxue-virtual-machine:/home/xiaoxue# cat test_python_mysql.py
import pymysql

db = pymysql.connect(
    unix_socket='/var/run/mysqld/mysqld.sock',  # 用户只能通过 Unix socket 连接
    host="localhost",
    user='root',
    password='123456',
    db='mysql',
    charset='utf8'
)

cursor=db.cursor()
cursor.execute('select version()')
data=cursor.fetchone()
print("数据库连接正确，该数据库版本是：%s" %data)
db.close()


root@xiaoxue-virtual-machine:/home/xiaoxue# python3 test_python_mysql.py
数据库连接正确，该数据库版本是：8.0.37-0ubuntu0.20.04.3
```

#### ==脚本案例5-Rsync起停脚本开发==

> `rsync` 是一个开源的文件传输工具，用于在不同主机之间或在本地同步文件和目录。它的全称是 "remote sync"，主要特点是支持增量传输、压缩、并且可以保持文件的权限、时间戳和符号链接等属性。

```sh
#参考官方的：/etc/init.d/rsync

root@xiaoxue-virtual-machine:/home/xiaoxue# bash /etc/init.d/rsync
Usage: /etc/init.d/rsync {start|stop|reload|force-reload|restart|status}
```

```sh
#! /bin/bash
#author:zxx
#-ne if条件的不等于，$#返回传递给脚本的参数个数 $0取得脚本文件名

if [ "$#" -ne 1]
	then
		echo "Usage: $0{start|stop|restart}"
		exit 1
fi

# 当用户选择是启动rsync
if [ "$1" = "start" ]
	then
		/usr/bin/rsync --daemon
		sleep 2
		# 验证端口是否启动了
		if [ `netstat -tunlp|grep rsync|wc -l` -ge 1 ]
			then
				echo "Rsync is started!"
				exit 0
		fi
elif [ "$1" = "stop" ]
	then
        killall rsync &>/dev/null
        sleep 2
        if [ `netstat -tunlp|grep rsync|wc -l` -eq 0 ]
            then
                echo "Rsync is stopped!"
                exit 0
        fi
elif [ "$1" = "restart" ]
	then
        killall rsync 
        sleep 1
        killpro=`netstat -tunlp|grep rsync|wc -l`
        /usr/bin/rsync --daemon
        sleep 1
        startpro=`netstat -tunlp|grep rsync|wc -l`
        if [ "$killpro" -eq 0 && "$startpro"  ]
            then
                echo "Rsync is restarted!"
                exit 0
        fi	
else
	echo "Usage: $0 {start|stop|restart|}"
	exit 1
fi
```





### 2.5 shell函数开发

```sh
#alias是指linux命令的别名
root@xiaoxue-virtual-machine:/home/xiaoxue# alias
alias cls='clear'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'

```

> shell函数定义的语法

```sh
# 标准shell函数定义
function 函数名(){
	函数体
	你想要执行的Linux命令
	return 返回值
}


# 偷懒写法
# 当使用function关键字时候，可以省略括号
function 函数名{
	函数体
	你想要执行的Linux命令
	return 返回值
}

# 超级偷懒写法
函数名(){
	函数体
	你想要执行的Linux命令
	return 返回值
}

# 执行该函数
函数名
```

#### 脚本案例1

```sh
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# cat func1.sh
#! /bin/bash

function chaochao(){

        cd /tmp/
        echo "我准备创建一个文件，且写入信息"
        echo "爱的魔力转圈圈" >> ./music.txt
        return 0
}

# 执行函数
chaochao

root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# bash func1.sh
我准备创建一个文件，且写入信息
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# bash func1.sh
我准备创建一个文件，且写入信息
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# bash func1.sh
我准备创建一个文件，且写入信息
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# cat /tmp/music.txt -n
     1  爱的魔力转圈圈
     2  爱的魔力转圈圈
     3  爱的魔力转圈圈
     4  爱的魔力转圈圈
```

#### 脚本案例2

> 函数定义和执行，分开在不同的文件中，Linux自带的诸多脚本，都是基于该形式使用的

```sh
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# cat my_func.sh
#! /bin/bash

chao(){
        echo "我是函数，我被执行~你真棒"
}

root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# source my_func.sh
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# set |grep ^chao
chao ()
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# chao
我是函数，我被执行~你真棒

```

```sh
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# cat func2.sh
#! /bin/bash

# 条件测试
[ -f ./my_func.sh ] && source  ./my_func.sh  || exit  # 这里要用source加载到当前shell环境中

# 执行函数
chao
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# cat my_func.sh
#! /bin/bash

chao(){
        echo "我是函数，我被执行~你真棒"
}
root@xiaoxue-virtual-machine:/home/xiaoxue/shell_program/learn_func# bash func2.sh
我是函数，我被执行~你真棒

```

#### 脚本案例3

> 

```sh
#! /bin/bash

function usage(){
        echo "Usage: $0 url"
        exit 1
}


# 功能检测URL
check_url(){
        wget --spider -q -0 /dev/null --tries=1 -T $1

        if [ "$?" -eq 0 ]
                then
                       echo "$1 is running..."
        else
                 echo "$1 is down..."
        fi

}


# 设立一个main函数，入口函数
main(){
        # 判断用户输入
        if [ "$#" -ne 1 ]
                then
                        usage
        fi

        check_url $1

}

main $*

```



> 服务启停管理脚本

```sh
xiaoxue@xiaoxue-virtual-machine:~/shell_program/learn_func$ ls /etc/init.d
acpid                apparmor          cron          gdm3         keyboard-setup.sh  open-vm-tools  procps                       speech-dispatcher  unattended-upgrades
alsa-utils           apport            cups          grub-common  kmod               openvpn        pulseaudio-enable-autospawn  spice-vdagent      uuidd
anacron              avahi-daemon      cups-browsed  hwclock.sh   mysql              plymouth       rsync                        ssh                whoopsie
apache2              bluetooth         dbus          irqbalance   network-manager    plymouth-log   rsyslog                      udev               x11-common
apache-htcacheclean  console-setup.sh  docker        kerneloops   nginx              pppd-dns       saned                        ufw
xiaoxue@xiaoxue-virtual-machine:~/shell_program/learn_func$

```

