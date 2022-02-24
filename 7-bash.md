## 第七章 认识学习BASH
### 一、硬件、核心和shell

什么是shell。shell是介于使用这和操作系统核心之间的东西。使用shell操作软件，和操作系统沟通，进而控制硬件，从使用者到shell到操作系统核心到硬件打通的过程。shell有很多版本，而bash的全称则是"Bourne Again SHell"代表这一版本。很多版本的shell，Linux默认是bash。在/etc/shells中记录支持哪些shell。在/etc/passwd中记录用户登录到系统中默认使用的shell环境。root是bash，其他会有一些奇怪的东西。登录终端到命令行就可以当作一个shell。

### 二、bash的功能

* history 功能

输入`history`或者`vim ~/.bash_history`可以查看历史命令。最多记录1000条。

* 命令文件名补全（tab功能）

tab补全是在bash才有的功能。命令第一个单词是命令补全，第二个是文件名补全。

* 命令别名设置(alias)
  
简化一个长命令，赋予一个别名。如`alias lm='ls -al|more'`。如果想要知道现在有哪些别名输入`alias`。取消一个别名`unalias lm`。

* 工作控制，前台后台控制(job control, foreground, background)

* 程序脚本(shell scripts)

* 查询命令是否是bash内置命令：type

使用`man bash`可以看到bash的说明文档，内容十分多。里面可以看到`cd`命令，这是因为bash内置了很多命令，这是其中之一。使用type可以知道一个命令是否是bash内置的命令。
```
type [-tpa] name
不加参数，会直接显示出是不是内置命令
-t  ：当加入 -t 参数时，type 会将 name 以下面这些字眼显示出他的意义：
      file    ：表示为外部指令；
      alias   ：表示该指令为命令别名所设置的名称；
      builtin ：表示该指令为 bash 内置的指令功能；
-p  ：如果后面接的 name 为外部指令时，才会显示完整文件名；
-a  ：会由 PATH 变量定义的路径中，将所有含 name 的指令都列出来，包含 alias

举例一：type ls
[root@ ~]# type ls
ls is aliased to `ls --color=auto'
[root@ ~]# type -a ls
ls is aliased to `ls --color=auto'
ls is /usr/bin/ls
ls is /bin/ls
[root@ ~]# type -t ls
alias

举例二：type cd
[root@  ~]# type cd
cd is a shell builtin
```

* 命令下达和快速编辑

如果命令太长一行写不下，可以使用反斜杠\进行跳脱，回车后在第二行出现大于号然后继续写命令。

快捷键快速编辑命令
|组合键|功能|
|:---|:---|
|ctrl+u, ctrl+k|前者是将光标前的命令删掉，后者是将光标后的命令删掉|
|ctrl+a, ctrl+e|前者是将光标移动到命令最前面，后者是将光标移动到命令最后面|

顺利在终端(tty)登录后，Linux就会根据/etc/passwd的设置给我们一个shell（通常是bash)，然后就可以操作shell了。

### 三、Bash的操作环境

1. 路径和命令指定顺序

如果命令下达，那么到底是哪个命令被执行了。命令的运行顺序按照如下排序：

* 以绝对或相对路径执行命令，如 `/bin/ls` 或 `./ls`
* 由别名(alias)找到命令执行
* 由bash内置的命令
* 通过$PATH这个变量的顺序找到的第一个命令

举例来说，执行`/bin/ls`结果没有颜色，而`ls`结果是有颜色的。因为前者是直接命令的下达，而后者是`ls --color=auto`的别名。执行`type -a ls`可以看执行顺序。

2. bash进入欢迎信息，/etc/issue, /etc/motd

结合使用进入终端显示一些信息

3. bash的环境配置文件

