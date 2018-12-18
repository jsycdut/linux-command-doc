# notify-send

## 名称

notify-send，一个发送桌面通知的程序。

## 概要
notify-send [选项] <消息概要> [消息正文]

## 描述
通过notify-send，你可以从命令行向一个用户发送桌面通知消息，用于提醒用户事件或者显示一些提示信息而不用打扰该用户。

## 选项

* -u --urgency=LEVEL
指定消息紧急程度，值为low, normal, critical之一

* -t, --expire-time=TIME
微秒，消息显示时间，不过会被Ubuntu忽略。

* -i, --icon=ICON[,ICON...]
文件名，用于信息的显示图标

* -c, --category=TYPE[, TYPE...]
执行消息提示类型

* -?, --help
显示提示信息

-h, --hint=TYPE:NAME:VALUE
指定要传送的额外的基础数据，有效类型为int, double, string, bye

## 参见
关于桌面通知参考可见，http://www.galago-project.org/specs/notification/

## 作者
此手册由Andre  Filipe de Assuncao e Brito <decko@noisemakers.org> ，Paul van  Tilburg  <paulvt@debian.org> 以及 Riccardo  Setti <giskard@debian.org> 编写，用于Debian项目，但也许会被用于其他项目。
