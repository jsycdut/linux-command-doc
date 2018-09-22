# PS

## 名称
ps - 报告当前进程情况（进程快照）

## 概要
ps [选项]

## 描述
ps显示被选中的活动进程的信息。这是一种当前进程时刻情况的描述（进程快照），由于进程情况时时刻刻在变，所以如果想要得到持续变化的进程情况，该用top命令而不是ps。

ps的选项部分比较复杂，接受三种风格的选项，如下

1. Unix风格， 选项必须以“-”开头，多个Unix风格的选项可以组合
2. BSD风格，  选项绝对不能以“-”开头，多个BSD风格的选项亦可以组合
3. GNU长选项风格， 选项以“--”开头，多个此风格的选项不可组合，只能一个个单独写

狗血的是，这三种风格的选项还可以自由混合使用，用是可以用，但是有可能发生冲突。三种风格中的选项有些是功能相同的，这是因为ps的标准和实现种类太多了。

注意到， ps -aux 不同于 ps aux，POSIX和Unix标准要求 ps -aux打印出所有属于名为x的用户的进程， 同时也要求打印出-a选项所指定的所有进程， 若名为x的用户不存在，出于兼容性，ps会将ps -aux理解为ps aux，这种自作主张的“转义”理解是靠不住的，不应该指望这种操作会给出你想要的结果。

默认情况下， ps会挑选出与当前用户拥有相同的EUID（有效用户ID）以及相同终端的所有进程。ps的显示内容包括，进程ID-PID，进程相关terminal-TTY，累计CPU时间-TIME，执行命令名称-CMD，这些输出条目是无序的。

BSD风格的选项会在ps的默认输出以外加上进程状态-STAT，并且会将默认的命令执行名称换为命令及其参数-COMMAND，这些都是可控的，受你的PS_FORMAT环境变量的影响，BSD风格的选项会将你拥有的其它terminal的进程也包含进来，这相当于无形中的添加了一种选择进程的规则：排除了其它用户的进程以及不在终端中的进程。但是，上面所述的那些附加影响会在某种情况下不被考虑，比如选项描述意义相同的时候，那时候-M将会被认为和Z选项相同。

接下来会举几个例子，进程选择选项是添加剂。默认的进程选择规则将会被抛弃，被选择出来的进程将会被加入显示内容中。一个进程只要满足给出的选择标准就会被显示。

# 例子
标准语法：选择系统中的所有进程
ps -e
ps -ef
ps -eF
ps -ely

BSD语法：选择系统中所有进程
ps ax
ps axu

打印进程树
ps -ejH
ps axjf

获取进程信息
ps -eLf
ps axms

获取安全信息
ps -eo euser,ruser,suser,fuser,f,comm,label
ps axZ
ps -eM

获取以root身份运行的所有进程
ps -U root -u root u

以用户定义的格式显示进程（注意逗号后面不能加空格）
ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
ps axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm
ps -Ao pid,tt,user,fname,tmout,f,wchan

打印syslogd的进程ID
ps -C syslogd -o pid=

打印PID为42的进程名称
ps -q 42 -o comm=

