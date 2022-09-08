# Shell基础汇总

## 1、shell概述

1、什么是shell

2、shell分类

## 2、shell脚本的执行方式

### 2.1 echo打印命令

- -e  支持反斜扛控制字符转义

- -n 取消输出后行末的换行符号


```
echo  hellword
echo "hello word"   #中间有空格用要加引号，如果行末有感叹号，必须加个空格。
```

在echo命令中如果使用了“-e”选项，则可以支持控制字符：

| 控制字符 | 作用                                                        |
| -------- | ----------------------------------------------------------- |
| \\       | 输出\\本身                                                  |
| \a       | 输出警告音                                                  |
| \b       | 退格键，也就是向前删除一个字符                              |
| \c       | 取消输出行末的换行符。和“-n”选项一致                        |
| \e       | ESCAPE键                                                    |
| \f       | 换页符                                                      |
| n        | 换行符                                                      |
| r        | 回车键                                                      |
| t        | 制表符，也就是Tab键                                         |
| v        | 垂直制表符                                                  |
| 0nnn     | 按照八进制ASCII码输出字符。其中0为数字零，nnn是三位八进制数 |
| xhh      | 按照十六进制ASCII码输出字符。其中hh是两位十六进制数         |


\ 转义符，如果你想打印出\，就必须打两个

```
[root@localhost ~]# echo -e \\
\
```

\b 退格键，也就是向左删除一个字符

```
[root@localhost ~]# echo -e "ab\bc"
ac
```

\t 制表符，也就是Tab键

```
[root@localhost ~]# echo -e "a\tb\tc\t"
a	b	c	
```

\v 垂直制表符

```
[root@localhost ~]# echo -e "a\vb\vc\v"
a
 b
  c
```

\n 换行符

```
[root@localhost ~]# echo -e "a\nb\nc"
a
b
c
```



### 2.2 脚本的执行方式

标准shell的格式

`vim hello.sh`

```
#!/bin/bash
# The first program
# Author：Yusa  mail:pokes@163.com

echo helloWord
```

执行shell脚本的两种方法：

给其执行权限（推荐）

```
chmod 755 hello.sh
./hello.sh
```

直接用`bash`调用执行

```
bash hello.sh
```

## 3、bash的基本功能

#### history 历史命令

```
history		  #查询历史命令
history -c    #清空历史命令，但是一般都清空
```

-c  清空历史命令

-w 把缓存中的历史命令写入历史命令保存文件。如果不手工指定历史命令保存文件，则放入默认历史命令保存文件中`~/.bash_history`中。

一般在linux初始化的时候，我们希望历史命令能保存的多一点，还需要去修改配置

```
vim /etc/profile
把里面的
HISTSIZE=1000   把这个数字改大，就可以保存多一点
```

#### 历史命令的调用

- 使用上下箭头调用以上的历史命令
- 使用“!n”重复执行第n条历史命令
- 使用“!!”重复执行上一条命令
- 使用“!字符串”重复执行最后一条以该字符串开头的命令
- 使用“!$”重复上一条命令的最后一个参数

#### bash的常用快捷键

- ctrl+A  把光标移动到行头
- ctrl+E   把光标移动到行末
- ctrl+U   删除或剪切光标之前的命令
- ctrl+K   删除或剪切光标之后的命令
- ctrl+Y  粘贴ctrl+U/K剪切的内容
- ctrl+R  在历史命令中搜索
- ctrl+Z  暂停并放入后台

#### 输入输出重定向

#### 输出重定向

标准输出重定向

```
正确命令 >test.log         #覆盖
正确命令 >>test.log        #追加

[root@localhost ~]# pwd >> test.log 
[root@localhost ~]# cat test.log 
/root
```

标准错误输出重定向

```
错误命令 2>               #覆盖
错误命令 2>>              #追加
```

正确输出和错误输出同时保存

```
命令 > test.log 2>&1      #覆盖
命令 >> test.log 2>&1     #追加
上面两种等价于,推荐用下面的
命令 &>test.log
命令 &>>test.log
```

把正确的和错误的信息分开保存

```
正确命令>>test1.log  错误命令 2>>test2.log
```

==注意：错误输出>左边不能有空格，有空格就报错==

#### 多命令顺序执行

| 多命令执行符 | 格式               | 作用                                              |
| ------------ | ------------------ | ------------------------------------------------- |
| ；           | 命令1；命令2       | 多个命令按前后顺序执行，命令之间没有任何逻辑关系  |
| &&           | 命令1 && 命令2     | 命令1正确执行之后，才执行命令2，反之则停止        |
| \|\|         | 命令1  \|\|  命令2 | 命令1执行不正确之后，才执行命令2，反之不执行命令2 |

举例说明：

```
[root@localhost ~]# ll && echo yes
总用量 16
-rw-------. 1 root root 1344 8月  22 2018 anaconda-ks.cfg
drwxr-xr-x  3 root root   45 9月   1 14:45 ceshi
-rw-r--r--  1 root root   36 9月   3 13:21 test
-rw-r--r--  1 root root    6 9月   3 13:22 test.log
-rw-r--r--  1 root root  308 9月   2 17:31 test.txt
yes
```

```
[root@localhost ~]# ll || echo yes
总用量 16
-rw-------. 1 root root 1344 8月  22 2018 anaconda-ks.cfg
drwxr-xr-x  3 root root   45 9月   1 14:45 ceshi
-rw-r--r--  1 root root   36 9月   3 13:21 test
-rw-r--r--  1 root root    6 9月   3 13:22 test.log
-rw-r--r--  1 root root  308 9月   2 17:31 test.txt
[root@localhost ~]#
```

```
[root@localhost ~]# ll && echo yes || echo no 
总用量 16
-rw-------. 1 root root 1344 8月  22 2018 anaconda-ks.cfg
drwxr-xr-x  3 root root   45 9月   1 14:45 ceshi
-rw-r--r--  1 root root   36 9月   3 13:21 test
-rw-r--r--  1 root root    6 9月   3 13:22 test.log
-rw-r--r--  1 root root  308 9月   2 17:31 test.txt
yes

在没有学习if之前可以使用上面的命令，判断命令是否执行正确。
```

#### grep命令

文件行提取命令

```
[root@localhost ~]# grep root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```



- -A n   列出符合条件的行，并列出后续的n行
- -B n   列出符合条件的行，并列出前面的n行
- -c 统计找到的符合条件的字符串的次数
- -i 忽略大小写
- -n 输出行号
- -v 反向查找
- --color 搜索出的关键字用颜色显示

```
[root@localhost ~]# grep -A 3 "root" /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
--
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin

```

意思就是找出含有root字符的行和后三行，一共找到两行含root的，共计输出8行，中间用--隔开了



#### bash中的其他特殊符号

| 作用  | 作用                                                         |
| :---: | ------------------------------------------------------------ |
| '   ' | 单引号。在单引号中的特殊符号，如“$”、“反引号”都没有特殊含义  |
| "  "  | 双引号。在双引号中的特殊符号都没有特殊含义，但是$（调用变量），反引号（引用命令），反斜扛（转义）例外 |
| \` \`  | 反引号。反引号括起来的内容是系统命令，在bash中会优先执行它。和$()作用一样，不过推荐使用$() |
| $() | 和反引号作用一样，用来引用系统命令。 |
| （） | 用于一串命令执行时，（）中的命令会在子shell中运行 |
| ｛｝ | 用于一串命令执行时，｛｝中的命令会在当前shell中执行。也可以用于变量变形与替换 |
| [ ] | 用于变量测试 |
| # | 在shell脚本中，#开头的行，或者注释 |
| $ | 用于调用变量的值，如需要调用变量name，则需要用$name的方式得到变量的值。 |
| \ | 转义符，跟在\之后的符号将失去特殊的含义，变为普通字符。 |

##### 单引号和双引号的使用

```
[root@localhost ~]# name=pokes
[root@localhost ~]# echo "$name"
pokes
[root@localhost ~]# echo '$name'
$name
[root@localhost ~]# 
单引号已经让$失去了调用变量的作用，$它现在只是一个普通的字符
```

##### 反斜扛的使用

```
[root@localhost ~]# shijian=date
[root@localhost ~]# echo $shijian
date
[root@localhost ~]# echo `$shijian`
2020年 09月 03日 星期四 14:51:50 CST
[root@localhost ~]# 
```

意思很明确，就是你在调用shijian这个变量的时候，这个变量把date这个命令执行完后的结果返回给你，而不是date本身。

##### 括号的使用

```
[root@localhost ~]# name=pokes
[root@localhost ~]# (name=test;echo $name)
test
[root@localhost ~]# echo $name
pokes
[root@localhost ~]# 
```

括号里面的重新赋值，不影响括号外面的值。小括号的作用就是在子shell运行完之后就结束了。有点像嵌套函数。

##### 大括号的使用

```
[root@localhost ~]# name=pokes
[root@localhost ~]# { name=test;echo $name; }
test
[root@localhost ~]# echo $name
test
[root@localhost ~]#
```

注意name后面有分号，大括号左右内有空格。

中括号在脚本中讲。

## 4、bash的变量和运算符

### 4.1 变量的命名规则

### 4.2 变量的分类

- 系统变量
- 环境变量
- 自定义变量

### 4.3 变量的查询

#### set命令

```
set -u   #如果设定此项，当你调用未声明的变量时会报错，默认情况下系统是不报错的
unset    #删除变量
```

变量的查询set命令和env命令，查询自定义变量要用set

#### env

```
[root@localhost ~]# env
XDG_SESSION_ID=135
HOSTNAME=localhost.localdomain
TERM=xterm
SHELL=/bin/bash		#你所使用的shell
HISTSIZE=1000		#历史命令条数
SSH_CLIENT=192.168.0.243 51828 22   #登录的IP
SSH_TTY=/dev/pts/2		#登录的客户端
USER=root				#登录的用户
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=01;05;37;41:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.axv=01;35:*.anx=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=01;36:*.au=01;36:*.flac=01;36:*.mid=01;36:*.midi=01;36:*.mka=01;36:*.mp3=01;36:*.mpc=01;36:*.ogg=01;36:*.ra=01;36:*.wav=01;36:*.axa=01;36:*.oga=01;36:*.spx=01;36:*.xspf=01;36:
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
LANG=zh_CN.UTF-8
HISTCONTROL=ignoredups
SHLVL=1
HOME=/root
LOGNAME=root
SSH_CONNECTION=192.168.0.243 51828 192.168.1.1 22
LESSOPEN=||/usr/bin/lesspipe.sh %s
XDG_RUNTIME_DIR=/run/user/0
_=/usr/bin/env

```

### 4.4 位置参数变量

```
[root@localhost ~]# vim pokes.sh
[root@localhost ~]# chmod 755 pokes.sh
```

```
#!/bin/bash

a=$1
b=$2
sum=$(( $a + $b ))
echo $sum
echo $0
```

```
[root@localhost ~]# ./pokes.sh 22 66
88
./pokes.sh
```

==(( ))用来转义，将字符串转义成数字==

接受键盘输入

read命令

```
read [选项] [变量名]
```





## 5、文本文件处理工具

### <font color='red'>grep行截取(重点)</font>

行过滤工具，用于根据关键字进行行过滤。

```
语法：grep [选项] '关键字' 文件名，例如：

[root@localhost ~]# grep root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
```

常用的选项：

```
grep -n root /etc/passwd    #-n显示行号
grep -ni root /etc/passwd   #-i忽略大小写
grep -ni ^root /etc/passwd  #^表示以root开头
grep -ni bash$ /etc/passwd  #$表示以bash结尾
grep -niv bash$ /etc/passwd #-v表示取反
grep -w hello /etc/passwd   #-w以单词搜索
```

### <font color='red'>cut列截取(重点)</font>

列截取工具 

```
[root@localhost ~]# head /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin

#截取以：为分隔符的第一列和第七列
[root@localhost ~]# cut -d: -f1,7 /etc/passwd   
root:/bin/bash
bin:/sbin/nologin
daemon:/sbin/nologin
adm:/sbin/nologin
lp:/sbin/nologin
sync:/bin/sync
shutdown:/sbin/shutdown
halt:/sbin/halt
mail:/sbin/nologin
operator:/sbin/nologin
games:/sbin/nologin
ftp:/sbin/nologin
nobody:/sbin/nologin
systemd-network:/sbin/nologin
dbus:/sbin/nologin
polkitd:/sbin/nologin
postfix:/sbin/nologin
sshd:/sbin/nologin
chrony:/sbin/nologin
ntp:/sbin/nologin
tcpdump:/sbin/nologin

[root@localhost ~]# cut -d: -f1,7 /etc/passwd|head   #head表示前10行
root:/bin/bash
bin:/sbin/nologin
daemon:/sbin/nologin
adm:/sbin/nologin
lp:/sbin/nologin
sync:/bin/sync
shutdown:/sbin/shutdown
halt:/sbin/halt
mail:/sbin/nologin
operator:/sbin/nologin

#截取从左边开始的的5个字符
[root@localhost ~]# cut -c1-5 /etc/passwd   #-c以字符截取
root:
bin:x
daemo
adm:x
lp:x:
sync:
shutd
halt:
mail:
opera
games
ftp:x
nobod
syste
dbus:
polki
postf
sshd:
chron
ntp:x
tcpdu
[root@localhost ~]# cut -c10- /etc/passwd   #从第10个字符开始到结束截取
0:root:/root:/bin/bash
:bin:/bin:/sbin/nologin
2:2:daemon:/sbin:/sbin/nologin
:adm:/var/adm:/sbin/nologin
lp:/var/spool/lpd:/sbin/nologin
0:sync:/sbin:/bin/sync
x:6:0:shutdown:/sbin:/sbin/shutdown
0:halt:/sbin:/sbin/halt
12:mail:/var/spool/mail:/sbin/nologin
x:11:0:operator:/root:/sbin/nologin
2:100:games:/usr/games:/sbin/nologin
50:FTP User:/var/ftp:/sbin/nologin
99:99:Nobody:/:/sbin/nologin
etwork:x:192:192:systemd Network Management:/:/sbin/nologin
:81:System message bus:/:/sbin/nologin
:999:997:User for polkitd:/:/sbin/nologin
:89:89::/var/spool/postfix:/sbin/nologin
:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
998:996::/var/lib/chrony:/sbin/nologin
38::/etc/ntp:/sbin/nologin
:72:72::/:/sbin/nologin



```

### sort排序

```bash
[root@localhost ~]# cat -n passwd   #-n是现实行号
     1	root:x:0:0:root:/root:/bin/bash
     2	bin:x:1:1:bin:/bin:/sbin/nologin
     3	daemon:x:2:2:daemon:/sbin:/sbin/nologin
     4	adm:x:3:4:adm:/var/adm:/sbin/nologin
     5	lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     6	sync:x:5:0:sync:/sbin:/bin/sync
     7	shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
     8	halt:x:7:0:halt:/sbin:/sbin/halt
     9	mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
    10	operator:x:11:0:operator:/root:/sbin/nologin
    11	games:x:12:100:games:/usr/games:/sbin/nologin
    12	ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
    13	nobody:x:99:99:Nobody:/:/sbin/nologin
    14	systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
    15	dbus:x:81:81:System message bus:/:/sbin/nologin
    16	polkitd:x:999:997:User for polkitd:/:/sbin/nologin
    17	postfix:x:89:89::/var/spool/postfix:/sbin/nologin
    18	sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
    19	chrony:x:998:996::/var/lib/chrony:/sbin/nologin
    20	ntp:x:38:38::/etc/ntp:/sbin/nologin
    21	tcpdump:x:72:72::/:/sbin/nologin
    
##以UID排序，默认是以升序排列
[root@localhost ~]# sort -n -t: -k3 passwd   #-n是以字符排序，-t是分隔符，-k是UID
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
[root@localhost ~]#

##以UID降序排序

[root@localhost ~]# sort -nr -t: -k3 passwd        #-r表示以降序排列
polkitd:x:999:997:User for polkitd:/:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
halt:x:7:0:halt:/sbin:/sbin/halt
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
sync:x:5:0:sync:/sbin:/bin/sync
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
root:x:0:0:root:/root:/bin/bash

[root@localhost ~]# sort -nr -t: -k3 passwd -o pass.txt    #-o表示不打印在屏幕，写入到文件中
[root@localhost ~]# ls
anaconda-ks.cfg  pass.txt  passwd


-u是去重

```

### uniq去除连续的重复行

省略

## 6、变量

### 变量的定义规则

### 变量的定义方式

#### 基本方式

#### 命令执行的结果赋值给变量

交互式定义变量(read)

| 选项 | 释义                                                     |
| ---- | -------------------------------------------------------- |
| -p   | 定义提示用户的信息                                       |
| -n   | 定义字符数(限制变量值的长度)                             |
| -s   | 不显示(不显示用户输入的内容)                             |
| -t   | 定义超时时间，默认单位为秒(限制用户输入变量值的超时时间) |

举例说明：

```bash
[root@localhost ~]# read -p "请输入你的IP地址:" IP   #交互式定义变量
请输入你的IP地址:

[root@localhost ~]# cat ip.txt 
10.10.30.99
[root@localhost ~]# read -p "请输入你的IP地址:" IP < ip.txt   #通过输入重定向给变量赋值
[root@localhost ~]# 
```

定义有类型的变量(declare)

目的：给变量做一些限制，固定变量的类型，例如：整形、只读

用法：declare 选项 变量名=变量值

| 选项 | 释义                       | 举例                                         |
| ---- | -------------------------- | -------------------------------------------- |
| -i   | 将变量看成整数             | declare -i A=123                             |
| -r   | 定义只读变量               | declare -r B=hollo                           |
| -a   | 定义普通数组；查看普通数组 |                                              |
| -A   | 定义关联数组；查看关联数组 |                                              |
| -x   | 将变量通过环境导出         | declare -x AAA=123456 等于 expore AAA=123456 |

举例说明：

```bash
[root@localhost ~]# declare -i A=123  #-i表示整形
[root@localhost ~]# echo $A
123
[root@localhost ~]# A=hello
[root@localhost ~]# echo $A
0

[root@localhost ~]# declare -r A=123  #-r表示只读
[root@localhost ~]# A=hello
-bash: A: 只读变量

```

### 简单的四则运算

默认情况下，shell只能支持简单的整数运算

运算内容：加+，减-，乘*，除/，求余数%

| 表达式 | 举例                          |
| ------ | ----------------------------- |
| $(())  | echo $((1+1))                 |
| $[]    | echo $[10-5]                  |
| expr   | expr 10/5                     |
| let    | n=1;let n+=1 等价于 let n=n+1 |



## 7、条件判断

### 条件判断语法格式

```bash
格式1：test 条件表达式
格式2：[ 条件表达式 ]     #注意空格
格式3：[[条件表达式]]     #支持正则表达式
```

### 条件判断的相关参数

问：你要判断什么？

答：我要判断文件类型，判断文件新旧，判断文件是否存在，判断字符串是否相等。。。。。

### 判断文件类型

红色字为重点，最常用

| 判断参数                    | 含义                                                         |
| --------------------------- | ------------------------------------------------------------ |
| <font color='red'>-e</font> | <font color='red'>判断文件是否存在（任何类型文件）</font>    |
| <font color='red'>-f</font> | <font color='red'>判断文件是否存在，并且是一个普通文件</font> |
| <font color='red'>-d</font> | <font color='red'>判断文件是否存在，并且是一个目录</font>    |
| -L                          | 判断文件是否存在，并且是一个软连接文件                       |
| -b                          | 判断文件是否存在，并且是一个块设备文件                       |
| -S                          | 判断文件是否存在，并且是一个套接字文件                       |
| -c                          | 判断文件是否存在，并且是一个字符设备文件                     |
| -p                          | 判断文件是否存在，并且是一个命名管道文件                     |
| -s                          | 判断文件是否存在，并且是一个非空文件（有内容）               |

举例说明：<font color='red'>判断文件是否存在？（重点）</font>

```
[root@localhost ~]# touch pokes.txt
[root@localhost ~]# test -e pokes.txt 
[root@localhost ~]# echo $?
0
[root@localhost ~]# test -e pokessss.txt
[root@localhost ~]# echo $?
1
[root@localhost ~]# [ -e pokes.txt ]     #中括号也是可以的，脚本中经常是使用中括号，记得括号两端的空格不能少
[root@localhost ~]# echo $?
0
```

### 判断文件权限

红色字为重点，最常用

| 测试选项                         | 作用                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| <font color='red'>-r 文件</font> | <font color='red'>判断该文件是否存在，并且是否该文件拥有读的权限，有读权限为真</font> |
| <font color='red'>-w 文件</font> | <font color='red'>判断该文件是否存在，并且是否该文件拥有写的权限，有写权限为真</font> |
| <font color='red'>-x 文件</font> | <font color='red'>判断该文件是否存在，并且是否该文件拥有执行权限，有执行权限为真</font> |
| -u 文件                          | 判断该文件是否存在，并且是否该文件拥有SUID权限，有SUID权限为真 |
| -g 文件                          | 判断该文件是否存在，并且是否该文件拥有SGID权限，有SGID权限为真 |
| -k 文件                          | 判断该文件是否存在，并且是否该文件拥有SBit权限，有SBit权限为真 |

举例说明：判断文件否有写和执行的权限？

```
[root@localhost ~]# ll
总用量 4
-rw-------. 1 root root 1241 9月  20 2020 anaconda-ks.cfg
-rw-r--r--. 1 root root    0 1月  28 10:51 pokes.txt
[root@localhost ~]# [ -w pokes.txt ]
[root@localhost ~]# echo $?
0
[root@localhost ~]# [ -x pokes.txt ]
[root@localhost ~]# echo $?
1
[root@localhost ~]# 
```

### 两个文件之间比较(新旧)

| 选项            | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| 文件1 -nt 文件2 | 判断文件1的修改时间是否比文件2的新，如果新则为真             |
| 文件1 -ot 文件2 | 判断文件1的修改时间是否比文件2的旧，如果旧则为真             |
| 文件1 -ef 文件2 | 判断文件1和文件2的Inode号是否一致，如果一致则为真。可以理解为两个文件是否为同一个文件，这个判断用于判断硬链接是很好的方法。 |





### 两个整数之间的比较

红色字为重点，最常用

| 测试选项                                | 作用                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| <font color='red'>整数1 -eq整数2</font> | <font color='red'>判断整数1是否和整数2相等，相等即为真</font> |
| 整数1 -ne整数2                          | 判断整数1是否和整数2不相等，不相等为真                       |
| <font color='red'>整数1 -gt整数2</font> | <font color='red'>判断整数1是否大于整数2，大于为真</font>    |
| <font color='red'>整数1 -lt整数2</font> | <font color='red'>判断整数1是否小于整数2，小于为真</font>    |
| <font color='red'>整数1 -ge整数2</font> | <font color='red'>判断整数1是否大于等于整数2，大于等于为真</font> |
| <font color='red'>整数1 -le整数2</font> | <font color='red'>判断整数1是否小于等于整数2，小于等于为真</font> |

举例说明：判断两个整数是否相等？

```bash
[root@localhost ~]# [ 23 -eq 24 ]
[root@localhost ~]# echo $?
1
[root@localhost ~]# [ 24 -eq 24 ]
[root@localhost ~]# echo $?
0
[root@localhost ~]# 
```

### 字符串的判断

| 选项                          | 作用                                 |
| ----------------------------- | ------------------------------------ |
| -z 字符串                     | 判断字符串是否为空，为空返回为真     |
| -n 字符串                     | 判断字符串是否非空，非空返回为真     |
| 字符串1==字符串2 (两个等号)   | 判断字符串是否相等，相等返回为真     |
| 字符串1！==字符串2 (两个等号) | 判断字符串是否不相等，不相等返回为真 |

举例说明：<font color='red'>判断两个字符串是否相等？（重点）</font>

```
[root@localhost ~]# [ "aa" == "aa" ]      #注意字符串，等号两端都有空格
[root@localhost ~]# echo $?
0
[root@localhost ~]# [ "aa" == "bb" ]
[root@localhost ~]# echo $?
1
```

### 多重条件判断

| 选项           | 作用                                             |
| -------------- | ------------------------------------------------ |
| 判断1 -a 判断2 | 逻辑与，判断1和判断2都成立，最终的结果才为真     |
| 判断1 -o 判断2 | 逻辑或，判断1和判断2有一个成立，最终的结果就为真 |
| !判断          | 逻辑非，使原始的判断式取反                       |

举例说明：判断11是否存在，再判断11是否大于23？

```
[root@localhost ~]# [ -n 11 -a 11 -gt 23 ]    #判断11是否存在，再判断11是否大于23，整数肯定是存在的。
[root@localhost ~]# echo $?
1
[root@localhost ~]# [ -n 11 -a 11 -gt 9 ]
[root@localhost ~]# echo $?
0
```

## 8、流程控制语句

### if语法结构

#### if结构

```bash
if [ 条件 ]；then
	command1
	command2
fi
```

 

#### if+else结构

注意事项：else分支可以没有，但是如果有了，中间到fi一定要有语句

```bash
if [ 条件 ]；then
		command1
		command2
	else
		command3
fi
```

举例：判断主机是否与远程主机互通？

```bash
#!/bin/bash
#该脚本用于判断主机是否在线
#pokes@163.com

#交互式定义变量，让用户自己输入IP地址

read -p "请输入你要ping的主机IP地址：" ip

#使用ping命令判断主机是否互通

ping -c3 $ip > null

if [ $? -eq 0 ];then
	echo "当前主机和远程主机互通"
  else
	echo "当前主机和远程主机不通"
fi
```

保存之后，记得给执行权限：`chmod +x ping.sh`



#### if+elif+else结构

多分支判断

如果条件1满足，执行command1；如果条件1不满足，再看条件2，如果条件2满足，则执行command2，如果条件1和2都不满足则执行command3

```bash
if [ 条件1 ]；then
		command1
	elif [ 条件2 ]
		command2
	else
		command3
fi
```

#### 层层嵌套结构

俗称：套娃

```bash
if [ 条件1 ]；then
		command1
		if [ 条件2 ]；then
		command2
		fi
	else
		if [ 条件3 ]；then
				command3
			elif [ 条件4 ]
				command4
			else
				command5
		fi
fi
```

拆分

```bash
###最外层
if [ 条件1 ]；then
		command1
		command2
	else
		command3
fi

###条件1中不满足
		if [ 条件2 ]；then
		command2
		fi

###
if [ 条件1 ]；then
		command1
	elif [ 条件2 ]
		command2
	else
		command3
fi
```

### case语句

多分支判断语句，但是和`if+elif+else结构`有些许的区别。case语句只能判断一种条件关系，而`if+elif+else结构`可以判断多种条件关系。

语句格式：

```
case $变量名 in
	"值1"
		如果变量的值等于值1，则执行程序1
		;;
	"值2"
		如果变量的值等于值2，则执行程序2
		;;
	"值3"
		如果变量的值等于值3，则执行程序3
		;;
	”都不是“
		如果都不是则执行其他程序。
		;;
esac
```

注意分号不能丢。

举例说明：

```bash
#!/bin/bash

echo "如果你想飞往上海，请输入1"
echo "如果你想飞往北京，请输入2"
echo "如果你想飞往深圳，请输入3"

read -p "请输入:" SHURUID

case "$SHURUID" in
        "1")
                echo "上海的机票已经发售"
                ;;
        "2")
                echo "北京的机票已经发售"
                ;;
        "3")
                echo "深圳的机票已经发售"
                ;;
        *)
                echo "输入错误,您只能输入1，2，3"
                ;; 
esac
```

### for循环

#### 语法1：shell写法

```
for 变量 in 值1 值1 值1...
	do
		command
	done
```

举例说明：<font color='red'>适用于固定循环次数</font>

```bash
#####1、 for直接赋值的情况，这种情况我们预先知道循环次数
[root@localhost ~]# vi shijian.sh

#!/bin/bash
#打印时间
#Author：pokes(pokes@163.com)

for time in 早上 中午 下午 晚上
	do
		echo "现在的时间是:$time"
	done
	
[root@localhost ~]# chmod +x shijian.sh 
[root@localhost ~]# ./shijian.sh 
现在的时间是:早上
现在的时间是:中午
现在的时间是:下午
现在的时间是:晚上

#####2、 使用命令赋值的情况

for var in `seq 1 9`
	do
		echo $var
		sleep 1
done

执行结果演示，其中sleep 1每秒中执行一次
[root@localhost:~]# sh forxunhuan2.sh 
1
2
3
4
5
6
7
8
9
```

举例说明：批量解压缩脚本？<font color='red'>这种方法适合不固定次数的循环</font>

```bash
#!/bin/bash
#批量解压缩脚本
#Author：pokes（pokes@163.com）

cd /lamp
ls *.tar.gz > ls.log
for i in $(cat ls.log)
	do
		tar -zxf $i &>/del/null
	done
rm -rf /lamp/ls.log
```

#### 语法2：C语言写法

它的优势在于双括号支持运算。

```
for ((变量;条件;自增减运算))
	do
		command
done
```

举例说明：

```bash
#!/bin/bash
#Author:pokes

for ((i=1;i<10;i++))
	do
		echo $i
done

执行效果：
[root@localhost:~]# ./forxunhuan.sh 
1
2
3
4
5
6
7
8
9
```

