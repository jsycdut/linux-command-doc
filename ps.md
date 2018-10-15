# PS

## 名称
ps - 报告当前进程情况（进程快照）

## 概要
ps [选项]

## 描述
ps显示被选中的活动进程的信息。这是一种当前时刻进程情况的描述（进程快照），由于进程情况时时刻刻在变，所以如果想要得到持续变化的进程情况，可以使用top命令。

ps的选项部分比较复杂，接受三种风格的选项，如下

1. Unix风格， 选项必须以“-”开头，多个Unix风格的选项可以组合
2. BSD风格，  选项绝对不能以“-”开头，多个BSD风格的选项亦可以组合
3. GNU长选项风格， 选项以“--”开头，多个此风格的选项不可组合，只能一个个单独写

这三种风格的选项还可以自由混合使用，用是可以用，但是有可能发生冲突。三种风格中的选项有些是功能相同的，这是因为ps的标准和实现种类太多了。

注意到， ps -aux 不同于 ps aux，POSIX（portable operating system interface，可移植操作系统接口）和Unix标准要求 ps -aux打印出所有属于名为x的用户的进程， 同时也要求打印出-a选项所指定的所有进程， 若名为x的用户不存在，出于兼容性，ps会将ps -aux理解为BSD风格的ps aux，这种自作主张的“转义”理解是靠不住的，不应该指望这种操作会给出你想要的结果。

常见ps输出条目含义如下
首先注意，不同风格的ps输出内容不尽相同

|条目|含义|
|:-:|:-:|
|PID|进程ID|
|TTY|对应进程的控制终端|
|TIME|进程使用的累计CPU时间|
|CMD|产生此进程的命令名称（不含参数）|
|STAT|进程状态（运行中、等待执行等）|
|START|进程开始执行到现在经历过的时间|
|%MEM|进程所用的内存（不是MB这种单位，而是20%这种百分比）|
|%CPU|进程所用的CPU（也是百分比）|
 
TTY显示为？的进程为守护进程，所谓的daemon，一般在系统启动的时候就开始运行了。

> TTY
>>TTY 就是执行命令的那个terminal。
terminal一般指终端，有真终端和伪终端之分，真终端就比如在Ubuntu上用Ctrl+Alt+F1这种进入的只能看见黑白字符的没有图形化的那种界面，伪终端就是你的图形化界面的时候召唤出那种类似于Ubuntu上的Gnome Terminal的东西，Ubuntu下的伪终端命名类似于`pts/*`，真终端命名类似于`tty*`，Mac下面的伪终端命名就类似于`ttys*`。

> Control terminal
>>你在terminal里面执行了某个命令，那么这个terminal就是那个命令的Control terminal，control terminal的意义在于你可以通过控制control terminal来控制它启动的程序，比如来个Ctrl+C之类的操作，或者可以通过标准输入和输出和程序进行互动等。

>各种ID：UID-EUID-SUID-SGID
>>这些ID都是为了权限管理
>> * UID：用户ID，每个用户都有个ID，root用户的id为0
>> * RUID：真实用户ID，谁登录到了shell，那么RUID就是谁，几乎永不会变
>> * EUID：有效用户ID，用于判定用户对文件和资源的访问权限，EUID只能变为SUID或者RUID
>> * SUID：Saved Set-User-Id，保存设置用户ID
>> * SGID：Saved Set-Group-Id，保存设置用户组ID


进程在执行过程中EUID是可能变的，因为进程可能需要访问某些自己没有权限访问的文件资源。注意上面说的ID都只是ID，不涉及文件权限，涉及文件权限的是rwx，以及set-user-id-bit, set-group-id-bit, sticky-bit。

>权限
一般书上都讲，权限就是rwx（至于什么421的就不说了），然后分三类，也就是文件所有者，所属组，其他人的读写执行权限，可以用9个二进制位来表示，然而，真相就是文件权限是12位，分别为SGT RWX RWX RWX，后面的RWX三连就是日常说的所有者，所属组，其他人权限。前面的SGT就是set-user-id-bit，set-group-id-bit，sticky-bit，sticky-bit用于控制文件的删除特性，有sticky-bit的文件只能由其所有者删除，set-user-id-bit 和 set-group-id-bit则一般设置在可执行文件身上，设置了这俩的可执行文件，在被非所属主的其他用户执行的时候，该进程的EUID和EGID会变为该可执行文件所属主的id和gid。一般来说，ls -l 显示出来的结果在权限上就9位，是没有12位的，但是又必须得显示，Linux用的办法就是借用x的位置，也就是在可执行权限的那一位做手脚，设置了set-user-bit，若该文件的所有者x位上为x，那么x会被替换为s，若x位上为空，那么该位上将会被赋值为S，set-group-id-bit类同，不过是在文件的所属组的x位上做手脚而，sticky-bit的话，借用的文件的其他人的x位，不过用的不是s，而是t，大小写的规则同前面二者。《Unix环境高级编程》

默认情况下， ps会挑选出与当前用户拥有相同的EUID（有效用户ID）以及由相同终端发起的所有进程。ps的显示内容包括，进程ID-PID，进程相关terminal-TTY，累计CPU时间-TIME，执行命令名称-CMD，这些输出条目之间是无序的。

BSD风格的选项会在ps的默认输出以外加上进程状态-STAT，并且会将默认的命令执行名称换为命令及其参数-COMMAND，这些都是可控的，受你的PS_FORMAT环境变量的影响，BSD风格的选项会将你拥有的其它terminal的进程也包含进来，这相当于无形中的添加了一种选择进程的规则：排除了其它用户的进程以及不在终端中的进程。但是，上面所述的那些附加影响会在某种情况下不被考虑，比如选项描述意义相同的时候，那时候-M将会被认为和Z选项相同。

接下来会举几个例子，ps命令指定选项之后，默认的进程选择规则将会被抛弃，被选择出来的进程将会被加入显示内容中。一个进程只要满足给出的选择标准就会被显示。

# 例子
标准语法：选择系统中的所有进程

* ps -e
 
* ps -ef
 
* ps -eF
 
* ps -ely

BSD语法：选择系统中所有进程

* ps ax
 
* ps axu

打印进程树

* ps -ejH
 
* ps axjf

获取进程信息

* ps -eLf
 
* ps axms

获取安全信息

* ps -eo euser,ruser,suser,fuser,f,comm,label
 
* ps axZ
 
* ps -eM

获取以root身份运行的所有进程

* ps -U root -u root u

以用户定义的格式显示进程（注意逗号后面不能加空格）

* ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
 
* ps axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm
 
* ps -Ao pid,tt,user,fname,tmout,f,wchan

打印名为syslogd的进程ID

* ps -C syslogd -o pid=

打印PID为42的进程名称

* ps -q 42 -o comm=

## 简单进程选择规则（选项）

* 注意
有些进程是跑在终端里面的，有些进程是跑在系统里面的。要弄明白自己是想要本终端的进程还是所有终端的，还是系统所有的，要把这个理念搞清楚。

* a 
```
BSD风格， 解除了BSD风格ps选项的仅挑选和当前用户有关的进程的限制，适用于BSD风格的选项以及ps被限制为BSD风格的时候，
选项里面没有出现“-”符号，也就是你当前使用的是BSD风格的ps。a选项的作用就是选择除了其他指定选项已选择的进程之外的选择，
换句话说，就是选择所有和终端有关的进程，或者在和x选项搭配的时候选择出所有的进程。
```
* -A 
```shell
Unix风格，选择所有的进程， 作用类同-e选项
```
* -a 
```shell
Unix风格，选择所有进程，但排除终端无关的进程以及会话领导者(session leader，第一个开启会话的进程，通常为一个shell)
```
* -d 
```shell
Unix风格，选择所有进程，但是排除会话领导者(session leader)
```
* -- deselect 

```shell
GNU长选项风格，选择除了那些实现某些特定条件的之外的进程（反选进程）， 作用类同与-N
```
* -e 
```shell
Unix风格，同-A，选择所有进程
```
* g 
```shell
BSD风格，选择所有进程，即使是session leader。此选项已过时且可能在未来的ps中不被支持
并且此选项仅在sunos4特性下才可用（基本可以放弃这个选项）
```
* -N 
```shell
Unix风格，反选进程。作用同前面的--deselect
```
* T 
```shell
BSD风格，选择与终端有关的所有进程，作用同不带参数的t选项
```
* r 
```shell
BSD风格，只选择在运行中的进程
```
* x 
```shell
BSD风格，解除BSD风格ps中的进程必须有对应的tty的限制（ps必须为BSD风格）
```
### 常用进程选择
* 列出本终端的所有进程
```shell
ps    # 默认ps
ps t  # t可换成T
```

* 列出所有终端的进程（不仅仅是本终端）
```shell
ps a   # 这个输出更加详细
ps -a  # 会排除terminal本身的shell进程
```

* 列出所有进程，不仅仅是终端进程
```shell
ps -e # -e 可以换成 -A

```

## 列表式规则选择进程

以下的选项（也就是说不是所有的选项都支持）支持一些列表式的参数，参数以空格分隔或者以逗号分隔，并且选项和参数可以使用多次，比如 ps -p "1 2" -p 3,4

-123 等同于 --pid 123

123  等同于 --pid 123

* -C cmdlist
```
cmdlist为进程名参数,选择进程名参数列表指定的进程，进程名为executable name
```
* -G grplist
```
grplist为真实组ID列表或者组名称列表,选择相应真实组ID的进程
```
* -g grplist

```
grplist为会话或者有效组名(Effective group name)，注意不是id。
```
* --group grplist
```
通过有效组ID来进行选择，grplist为EGID或者名称，效果同-G
```
* p pidlist
```
通过pid列表进行选择，选项同-p 和 --pid（这俩为Unix风格）
```
* --ppid pidlist
```
通过ppid进行选择，选择那些父id是ppid的进程
```
* q pidlist
```
通过pid进行选择（快速模式），同-q --quick-pid选项
```
* -q pidlist
```
通过pid进行选择（快速模式）。ps仅会读取pidlist里面的pid的必要信息，不会使用附加的过滤规则。
pid的顺序是无序的，此选项使用的时候，不允许使用额外的挑选进程的选项，排序也不允许，进程树的列出也不允许
同-q和q选项
```

* --quick-pid pidlist

```
同-q 和 q选项

```
* -s sesslist

```
通过session ID进行选择，同-s选项
```
* t ttylist
```
通过tty进行选择，和-t --tty选项类似，但是可以让ttylist置空来代表和ps命令有关的那个tty
使用T选项被认为要比用t在使用空ttylist列表的时候要更纯净些
```
* --tty ttylist

```
通过terminal进行选择，同-t和t
```
* U userlist

```
通过EUID或者用户名进行选择，EUID和进程有关，当用户的存取文件权限被用于进程存取文件的权限时，进程的EUID就是该用户的id。
选项同-u 和 --user
The effective user ID describes the user whose file access permissions are used by the process 
```
* -U userlist

```
通过RUID或者用户名来进行选择，RUID用于识别创建进程的用户
The real user ID identifies the user who created the process
```
* -u userlist
```
同U 和 --user
```
* --User userlist
```
通过RUID或者用户名进行选择，同-u 
```
* --user userlist

```
通过EUID或者用户名进行选择，同-u和U选项
```
## 常用选择

* 选出指定名称的进程
```shell
ps -C sslocal        # 选出名为sslocal的进程
ps -C sslocal python # 选出名为sslocal，python的进程
```

* 选出某个用户组的进程(同样注意RGID EGID 以及有效组名)
```shell
ps -G 1000 0 # 选出RGID为1000和0这两个用户组的开启的进程
```

* 选出进程ID为指定值的进程
```shell
ps -p 1 2 3  # 选出pid为1， 2， 3的进程
```

* 选出指定terminal的进程
```shell
ps -t tty7   # 选出tty7的进程
```

* 选出指定用户的进程（注意EUID和RUID的区别和使用）
```shell
ps -U jsy    # 选出用户jsy开启的进程（RUID）
ps U jsy     # 选出EUID为jsy的进程
```

---
* 小结
可以通过进程id，进程父级id，用户名，EUID，RUID，组名，EGID，RGID，tty进行进程选择。
---

## 输出格式控制


