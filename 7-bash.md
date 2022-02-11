## 第七章 认识学习BASH
### 一、硬件、核心和shell

什么是shell。shell是介于使用这和操作系统核心之间的东西。使用shell操作软件，和操作系统沟通，进而控制硬件，从使用者到shell到操作系统核心到硬件打通的过程。shell有很多版本，而bash的全称则是"Bourne Again SHell"代表这一版本。很多版本的shell，Linux默认是bash。在/etc/shells中记录支持哪些shell。在/etc/passwd中记录用户登录到系统中默认使用的shell环境。root是bash，其他会有一些奇怪的东西。登录终端到命令行就可以当作一个shell。

### 二、bash的功能

* history 功能

输入`history`或者`vim ~/.bash_history`可以查看历史命令。最多记录1000条。

* 命令文件名补全（tab功能）

tab补全是在bash才有的功能。命令第一个单词是命令补全，第二个是文件名补全。

* 命令别名设置(alias)

* 工作控制，前台后台控制(job control, foreground, background)

* 程序脚本(shell scripts)


