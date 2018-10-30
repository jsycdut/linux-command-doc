# kill

## 名称
kill-对进程发送信号

## 概要
kill [选项] <pid> [...]

## 描述
kill命令默认发送的信号是TERM，使用-l或者-L选项可列出所有可使用的信号，一些特别常用的信号如HUP, INT, KILL, STOP, CONT, 0等。

一个信号可以用三种方法来表达，比如对于kILL信号，就可以使用-9， -SIGKILL或者-KILL来表达（手册在这里讲解的不清楚，我的理解是比如你要发送KILL信号，可以直接kill -KILL，但你也可以在前面加个SIG成为-SIGKILL，又因为KILL信号的数字表达是9，所以你可以用-9，其他的进程信号类似）。

负数的pid值可用于选择所有进程组， 详见ps命令输出的PGID一列；为-1的pid尤其特殊，代表着除kill进程本身和init进程之外的所有进程。

## 选项
* <pid> [...] 
```
向pid列表中的所有进程发送信号
```
* -signal
* -s signal 
* --signal signal 
```
这三者全是意为发送signal指定的信号，信号可以数字或者名称的形式，signal的含义在signal(7)手册中有讲解。
```
* -l, --list[signal]
```
列出signal的名称，此选项有可选参数，可以将signal数字转为signal名称，，或者反过来，参数为signal名称的时候将其转为数字。
```

* -L, --table
```
用nice表格的形式列出信号名称,(可能支持的不太好)
```

**注意，shell里面可能有内置的kill命令，这时候需要以绝对路径使用kill，比如/bin/kill**

## 例子

* kill -9 -1
杀掉你所有能杀的进程

* kill -l 11
将数字11转换为sinal名称

* kill 123 543 2341 3453
将默认的SIGTERM发送给上面列表中的进程

## 参阅
kill(2), killall(1), nice(1), pkill(1), renice(1), signal(7), skill(1)

## 标准
本命令符合适当的标准，但-L选项对Linux的版本有要求(比如我的Ubuntu16.04使用-L就不好使，因为kill默认是shell内置命令)

