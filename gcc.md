![gcc](https://www.itjungle.com/tfh/tfh101415-story01-fig01.pn://www.embarcados.com.br/wp-content/uploads/2014/05/imagem-de-destaque-61.png)
# gcc

## 名号
GCC - GNU C和C++编译器。

## 概要
```
gcc [-c|-S|-E] [-std=standard]
    [-g] [-pg] [-Olevel]
    [-Wwarn...] [-Wpedantic]
    [-Idir...] [-Ldir...]
    [-Dmacro[=defn]...] [-Umacro]
    [-foption...] [-mmachine-option...]
    [-o outfile] [@file] infile...
```
这里仅列出最有用的选项，见以下内容备忘。g++的大多数选项同gcc。

## 描述
当你调用gcc之时，它做的常规操作包括预处理，编译，汇编以及连接。总体的大选项使得你可以停止当前gcc构建进程到某一个中间阶段，比如-c选项就不会调用连接器，输出文件也保持在编译器产生的对象文件。（这有点强行停车的感觉）

其余的选项是针对那些“阶段”的。某些选项控制处理器，某些则控制编译器本身。然而另外的某些选项控制预处理器，某些则是控制连接器，大多选项都未在此处列出，因为你鲜有用到他们的机会（说句实话，只有那些超底层大神才有机会去接近那些神之操作，我等凡人还是老实点把，此处手动无奈）

大多gcc的命令行参数都对c程序有效，当某选项仅对c++之类的语言有效的时候，会明确指出的。当该选项没有明确指出的时候，你可以认为它对所有支持的语言都行之有效。



