## 第五章 文件和文件系统的压缩，备份与打包

### 一、压缩文件的用途和技术

有时候文件太大，发邮件不出去。有时候会遇到传文件需要光盘的情况，但是光盘放不下。有时候会定时备份一些数据，但是数据量很大，占用了很大的磁盘容量。这时候压缩技术就派上用场了。

什么是压缩技术呢。一般计算机使用字节(Byte)作为基础存储单位。一个字节由8比特(bit)组成，8个二进制位。比如数字1，字节里左侧7位是0，最右侧是1。左侧的0可以看作是一种空，利用某种办法把左侧的空白利用起来就节约了空间。

还有一种是结算出某种循环存储。比如存储了100个1，使用逻辑描述这里是100个1，而不是真的去存储100个1。这样也能大大压缩空间。技术参考[http://www.ietf.org/rfc/rfc1952.txt](http://www.ietf.org/rfc/rfc1952.txt)

很多网站使用传输压缩的数据，基本上浏览网页时间都会花在数据传输上，CPU运算时间一比是很小的。举个例子来说， Linux 3.10.81 （CentOS 7 用的延伸版本） 完整的核心大小约有 570 MB 左右，而由于核心主要多是 ASCII code 的纯文本体态文件，这种文件的“多余空间”最多了。而一个提供下载的压缩过的 3.10.81 核心大约仅有 76MB 左右。

### 二、Linux上常见的压缩命令

Linux上压缩名大多是".tar, .tar.gz, .tgz, .gz, .Z, .bz2, *.xz"，在Linux本质上扩展名基本是没用的，但是为了让人类看清是什么文件类型而做的区分，不然就不知道该用什么软件进行解压了。列出几个常见的压缩文件扩展名：
```
*.Z                 compress程序压缩的文件
*.zip               zip程序压缩的文件
*.gz                gzip程序压缩的文件
*.bz2               bzip2程序压缩的文件
*.xz                xz程序压缩的文件
*.tar               tar程序打包过的数据，没有压缩过
*.tar.gz            tar程序打包过的数据，被gzip压缩过
*.tar.bz2           tar程序打包过的数据，被bzip压缩过
*.tar.xz            tar程序打包过的数据，被xz压缩过
```
Linux上常见的是gzip, bzip2, 以及最新的xz。为了支持windows上的zip，Linux上很早也支持了zip。gzip由GNU开发，取代淘汰了的compress。后来GNU又开发出更好的bzip2和xz。但是压缩只能针对单个文件，有很多文件的话挨个压缩解压缩是很麻烦的。所以tar打包就出来了。tar可以把很多文件打包成一个文件，目录也可以。tar本身是没有压缩功能的，后来GNU把tar和压缩功能集合到一起了。

1. gzip, zcat/zmore/zless/zgrep

gzip可以解压开compress, zip, gzip等压缩的文件。gzip压缩后后缀是.gz。
```
gzip --help                     # 查看gzip命令参数
-c  ：将压缩的数据输出到屏幕上，可通过数据流重导向来处理；
-d  ：解压缩的参数；
-t  ：可以用来检验一个压缩文件的一致性～看看文件有无错误；
-v  ：可以显示出原文件/压缩文件的压缩比等信息；
-#  ：# 为数字的意思，代表压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！默认是 -6

举例：找到/etc下最大的文件，复制到/tmp下使用gzip压缩
ls -ldrSh /etc/*                # 可以回顾下ls命令的使用方法

cp /etc/services /tmp/services

cd /tmp

gzip -v services
services:	 79.7% -- replaced with services.gz

ll /etc/services /tmp/services.gz
-rw-r--r--. 1 root root 655K Jun  7  2013 /etc/services
-rw-r--r--  1 root root 133K Jan 27 14:02 /tmp/services.gz
```
使用gzip时，原文件就会被压缩成为.gz的文件，这点和windows不一样。gzip一般在windows上被压缩软件解压。
```
举例二：将上一例压缩文件读出来
zcat services.gz
# 因为原本是文本文件，所以可以用zcat/zless/zmore读取

举例三：将第一例中的文件解压缩
gzip -d services.gz
# 加-d解压，解压会把gz文件删除

举例四：将解开的文件用最佳压缩比压缩，并保留原始文件
gzip -9 -c services > services.gz

举例五：找出例四中的http关键字在哪行
zgrep -n 'http' services.gz
```

gzip 的压缩已经是优化过的了。虽然提供1-9级压缩等级，使用默认的6已经是很好的了。例四中的-c 和 >, -c可以把本来压缩的内容输出到屏幕上，而>把输出的内容重定向到services.gz这个文件中。文件名可以随便写，但最好是按照规范写gzip的压缩名称。关于>会在bash章节再次提及。

cat/more/less 用于读取文本文件，而zcat/zmore/zless用于读取对应压缩文本文件。想要寻找关键字的话，使用zgrep是个很好的办法。使用znew可以把.Z文件转为.gz类似文件。

2. bzip2, bzcat/bzmore/bzless/bzgrep

如果说gzip是为了取代compress而出现，那么bzip2则是为了取代gzip而出现。bzip2比gzip的压缩比还好，用法几乎相同。
```
bzip2 --help
bzcat **.bz2

选项与参数：
-c  ：将压缩的过程产生的数据输出到屏幕上！
-d  ：解压缩的参数
-k  ：保留原始文件，而不会删除原始的文件喔！
-z  ：压缩的参数 （默认值，可以不加）
-v  ：可以显示出原文件/压缩文件的压缩比等信息；
-#  ：与 gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！

举例一：将/tmp/services用bzip2压缩
bzip2 -v services
services:  5.409:1,  1.479 bits/byte, 81.51% saved, 670293 in, 123932 out.

[root@mail tmp]# ls -lh services*
-rw-r--r-- 1 root root 122K Jan 27 14:35 services.bz2
-rw-r--r-- 1 root root 133K Jan 27 14:47 services.gz
# 压缩比比gzip的9级还要好

举例二：将例一种的压缩文件读出来
bzcat services.bz2

举例三：解压
bzip2 -d services.bz2

举例四：将文件使用最佳压缩比压缩保留原文件
bzip2 -9 -c services > services.bz2

```
bzip2的选项和gzip一模一样，但拥有更高的压缩比。不过对于容量大的文件，bzip2的压缩时间比gzip耗费更多。

3. xz, xzcat/xzmore/xzless/xzgrep

虽然bzip2已经有很好的压缩比，但是有不满意的声音，后来推出压缩比更好的xz。用法和gzip和bzip一样。
```
xz --help
xcat **.xz
选项与参数：
-d  ：就是解压缩啊！
-t  ：测试压缩文件的完整性，看有没有错误
-l  ：列出压缩文件的相关信息
-k  ：保留原本的文件不删除～
-c  ：同样的，就是将数据由屏幕上输出的意思！
-#  ：同样的，也有较佳的压缩比的意思！

举例一：将/tmp/services用xz压缩
xz -v services
services (1/1)
  100 %        97.3 KiB / 654.6 KiB = 0.149

[root@mail tmp]# ll -h services*
-rw-r--r-- 1 root root 122K Jan 27 14:51 services.bz2
-rw-r--r-- 1 root root 133K Jan 27 14:47 services.gz
-rw-r--r-- 1 root root  98K Jan 27 14:35 services.xz

举例二：列出文件压缩信息，
xz -l services.xz
Strms  Blocks   Compressed Uncompressed  Ratio  Check   Filename
    1       1     97.3 KiB    654.6 KiB  0.149  CRC64   services.xz

xcat services.xz

举例三：解压缩
xz -d services.xz

举例四：保留原文件压缩
xz -k services

```
xz 压缩比最好了。但相同的问题，xz压缩使用的时间比gzip长太久。就是取舍了，但是时间上的差异到底有多少有待取整，可以测试一次。
 
### 三、打包命令：tar

虽然gzip, bzip2, xz也能针对目录压缩，但是是对目录下的每个文件分别压缩。将多个文件或目录打包成一个文件的就是 `tar` 了。`tar` 能在将文件目录打包的同时然后压缩。

1. tar

tar的选项和参数很多，只说几个常用的，更多用法 `man tar`
```
tar [-z|-j|-J][cv][-f 带创建文件名] filename   # 打包和压缩
tar [-z|-j|-J][tv][-f 既有的tar文件]           # 查看已有的tar文件
tar [-z|-j|-J][xv][-f 既有的tar文件] [-C]      # 解压缩

选项与参数：
-c  ：创建打包文件，可搭配 -v 来察看过程中被打包的文件名（filename）
-t  ：察看打包文件的内容含有哪些文件名，重点在察看“文件名”就是了；
-x  ：解打包或解压缩的功能，可以搭配 -C （大写） 在特定目录解开
      特别留意的是， -c, -t, -x 不可同时出现在一串命令行中。
-z  ：通过 gzip  的支持进行压缩/解压缩：此时文件名最好为 *.tar.gz
-j  ：通过 bzip2 的支持进行压缩/解压缩：此时文件名最好为 *.tar.bz2
-J  ：通过 xz    的支持进行压缩/解压缩：此时文件名最好为 *.tar.xz
      特别留意， -z, -j, -J 不可以同时出现在一串命令行中
-v  ：在压缩/解压缩的过程中，将正在处理的文件名显示出来！
-f filename：-f 后面要立刻接要被处理的文件名！建议 -f 单独写一个选项啰！（比较不会忘记）
-C 目录    ：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。

其他后续练习会使用到的选项介绍：
-p（小写） ：保留备份数据的原本权限与属性，常用于备份（-c）重要的配置文件
-P（大写） ：保留绝对路径，亦即允许备份数据中含有根目录存在之意；
--exclude=FILE：在压缩的过程中，不要将 FILE 打包！
```
直接记忆的是如下:

* 压缩：tar -jcv -f filename.tar.bz2 directory
* 观察：tar -jtv -f filename.tar.bz2
* 解压缩：tar -jxv -f filename.tar.bz2 -C directory

filename.tar.bz2 后缀完全是自己加上的，tar不会做处理。但是加上是很重要的，谁看到都会明白是怎么压缩的。z对应.tar.gz, j对应.tar.bz2, J对应.tar.xz。

-f filename是连接在一起的。有时候文章会写成`tar -jcvf filename`，这样没问题。理论上参数是可以变顺序的，这里f如果不是紧挨着filename就会有问题，如`tar -jvfc filename`就导致文件名变成c，因为文件名是f后面的内容。所以为了不发生这样的问题把-f单独摘出来。

经常备份/etc是好习惯。现在备份测试
```
time tar -zpcv -f /root/etc.tar.gz /etc
tar: Removing leading `/' from member names   # 注意这个警告信息

...
real    0m0.865s            # 花费0.9s
user    0m0.830s
sys     0m0.044s
# 由于加上 -v 这个选项，因此正在作用中的文件名就会显示在屏幕上。
# 如果你可以翻到第一页，会发现出现上面的错误讯息！下面会讲解。
# 至于 -p 的选项，重点在于“保留原本文件的权限与属性”之意。

time tar -jpcv -f /root/etc.tar.bz2 /etc
real    0m1.793s
user    0m1.752s
sys     0m0.069s

time tar -Jpcv -f /root/etc.tar.xz /etc
real    0m10.278s
user    0m10.150s
sys     0m0.132s

# 相比xz花费时间最多

[root@VM-4-15-centos ~]# ll -Sh  /root/etc*
-rw-r--r-- 1 root root 5.3M Jan 27 19:13 /root/etc.tar.gz
-rw-r--r-- 1 root root 4.0M Jan 27 19:16 /root/etc.tar.bz2
-rw-r--r-- 1 root root 3.3M Jan 27 19:16 /root/etc.tar.xz

# 压缩使用时间与文件大小和预想中顺序一致
```
xz压缩最小，但是时间相差好几倍。所以使用时真的需要考虑时间成本。加上-p是要保存原文件的权限和属性。备份重要的系统文件时，要把权限也复制出来。

* 查阅压缩文件，和压缩文件名有没有根目录的意义
```
tar -jtv -f /root/etc.tar.bz2

...
rw-r--r-- root/root        51 2020-09-16 04:10 etc/centos-release-upstream
-rw-r--r-- root/root       317 2019-12-10 11:21 etc/dracut.conf
-rw-r--r-- root/root      3186 2020-12-10 10:57 etc/rsyslog.conf
...
```
如果加上v，详细文件权限/属性就显示出来。如果只显示文件名的话把v去掉就行。观察可以发现一个有趣的现象，每个文件名的根目录/都没了。这也是压缩时显示的告警信息**tar: Removing leading `/' from member names**。

去除根目录主要是为了安全。在tmp中解压开的话，解压的文件就是/tmp/etc/xx这样。但是如果有根目录的话，解压开的数据就是/etc/xx就直接到/etc把现有数据覆盖掉了。如果需要保留根目录的话，压缩时使用-P参数。但是最好不要这么做。

* 解压缩
```
tar -jxv -f /root/etc.tar.bz2 -C /tmp
```
如果不加-C参数的话，就会在当前目录解压缩。然后`rm -rf /tmp/etc`，删除掉解压开的etc。这个命令很危险，注意不要把/etc给删掉了，删了系统就死了。

* 解压单一文件的方法

```
1. 第一步先找到要解压缩的文件
tar -jtv -f /root/etc.tar.bz2 | grep shadow
2. 把要解压的文件写在后面, 注意要解压的文件名，没有根目录。解压的当前目录
tar -jxv -f /root/etc.tar.bz2 etc/shadow
```


* 打包目录，但是不包含目录下几个文件的做法


