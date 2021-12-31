## 一、硬盘
1. MBR和GPT格式分区  
	主要分区预留1234和延伸。没有234延伸也是从5开始，sda1,sda5,sda6这种的。
2. BIOS和UEFI开机程序
	
## 二、基础功能
1. 常用信息
   *  ~ 代表用户主文件夹
   * locale 本地信息命令
   * date cal 时间日期；timedatectl
   * bc 计算器 quit退出
   * --help (-h 可能会模糊的被使用的，但是--help是精确的唯一的)
   * nano 命令文件编辑。使用vim代替，略过
2. `man [command]`, manual 帮助文档。command后括号中标记的数字代表一些含义，如NULL(4)4代表文件。
    * 1-shell环境中可以执行的命令或文件；
    * 5-配置文件或是某些文件的格式；
	* 8-系统管理员可以使用管理指令  
      - `man -f man` 查看属性
  	  - 多个参数把数字放在中间。
		``` 
		man 1 man
		```
	- `man -k man` 以man为关键字查询manual, like。 也可以使用 `whatis man`
	- 在/usr/share/doc/下有一些有用的文档。/usr/share/info下也有，info命令也可
3. 正确关机</br>
   1. `shutdown`
       * `man shutdown` 中有命令的介绍
       * `sync` 将内存中的数据写入到硬盘中，关机前做一个保存。</br>
       * 然后使用 **`shutdown`** 命令关机，默认是 `shutdown -h +1` 一分钟后关机。-h --halt多久后执行关机。
       * `shutdown -h 1:20` 1点20关机
	1. `reboot`
	   * reboot 和 poweroff 和 halt 一样，可以 `man reboot` 中看出
	   * 重启时鸟哥的操作 `sync; sync; sync; reboot`
	3. 和systemctl相关，作为systemctl后接指令

## 三、文件与目录
文件有三个身份 `owner/group/others` , 有`read/write/execute` 等权限</br>

1. 文件的权限

   * `ls -al` 展示文件夹下文件信息。从左到右分别是 权限、链接、拥有者、群组、文件大小、修改日期、文件名
   ![fileInformation](./picture/fileInformation.png)
   * 权限项有十个字符，例如 `drwxr-xr-x`
      + 第一个字符表示"目录，文件或链接"
         - 为 \[d\] 时表示目录
         - **为 \[-\] 时表示文件**
         - 为 \[l\] 时表示链接
         - 为 \[b\] 时表示设备文件里可供存储的周边设备（可随机存取设备）
         - 为 \[c\] 时表示设备文件里的序列埠设备，如键盘鼠标（一次性读取设备）
      + 接下来的字符中以三个为一组，且均为"rwx"三个参数的组合。</br>
         - \[r\] 表示 "read" ; \[w\] 表示 "write" ; \[x\] 表示 "execute"。三个参数前后位置不会变化
         - 没有权限则用 \[-\] 代替
         - 第一组文件拥有者的权限，第二组加入此群组账号的权限，第三组非本人没有加入群组其他账号的权限
   * 链接数。表示有多少文件文件名链接到此节点
   * 文件名前如果有点 \[.\] 说明是隐藏文件
   * 如果是文件夹的话，没有执行权限是进不去文件夹的
2. 修改文件属性和权限</br>
   chgrp, chown, chmod。修改文件组用户权限的命令。-R 选项可以递归修改文件夹。</br>
   chown 还可以一同修改组。chown \[-R\] 帐号名称:群组名称 文件或目录
   * chmod
      + 数字模型对应权限</br>
         **read:4, write:2, x:1** 配合owner/group/others的顺序就可以计算了</br>
         例如owner全部，group读和执行，other没有权限那么相加计算完就是 730</br>
         `chmod -R 730` 递归计算权限
      + 字母模型</br>
         **`chmod u=rwx,go=rx file`** user赋予rwx权限，group和others拥有rx权限</br>
         `chmod a+r` 所有人添加r权限</br>
         `chmod a-w` 所有人去掉w权限</br>
3. 目录权限的意义
   * 对文件来说。r是阅读。w是修改的权限（不含删除）。x是执行的权限，区别于windows依靠扩展名进行判断。**是否可删除需要看有没有上级目录的w的权限**
   * 文件夹来说。r是读文件夹下文件名的权限。w是对文件夹下**增、删、改、被移动，更名**的权限。x是进入文件夹的权限，否则不能cd进去
4. 文件名和扩展名
   * **linux下一切皆文件**
   * 各种类型
      + 文件
         - ASCII文件。可以被cat
         - 二进制文件。cat命令就是二进制文件
         - data。固定格式的文件
      + 目录。权限开头为\[d\]
      + 链接。权限开头为\[l\]
      + 设备与设备文件(device)。系统周边和存储相关的文件，一般在/dev下。
         - 区块(block)设备文件。如硬盘等。权限开头为\[b\]
         - 字符(character)设备文件。如键盘鼠标等。权限开头为\[c\]
      + 数据接口(sockets)文件。网络上的数据沟通。一般在/run或/tmp下。权限开头为\[s\]
      + 数据传输(FIFO, pipe)。队列读文件(first in first out)。权限开头为\[p\]</br>
      可以通过 `man fifo` , `man socket` 查看信息
   * 文件扩展名</br>
      文件可不可以执行一个是x，另一个是本身是否可以被执行。如文本即使有x也不能被执行，cat命令去掉x后也不能被执行</br>
      一般扩展名有：</br>
      * .sh。脚本或批处理文件。用shell写成。
      * Z, .tar, .tar.gz, .zip, .tgz。各种压缩文件
   * 文件名长度限制。依照文件系统设置，在.ext2/.ext3/.ext4文件系统下是255Bytes
   * 文件命名限制。最好不要包含特别特殊的符号如 ``? > <br ; & ! [ ] | \ ' " ` （ ） { }`` 同时避免使用 `+ -` 开头，`.` 开头的是隐藏文件
5. 文件和目录拥有的默认权限和隐藏权限</br>
   * 默认权限
      * 输入 `umask -S` 查看当前使用使用者创建文件时的默认权限。
      * 输入 `umask` 出现4个数字，后3个数字和权限相关。例如0022是指三个角色的权限要减去的权限。</br>
      **创建文件时默认权限（-rw-rw-rw-）**，减去-----w--w-, 剩下-rw-r--r--</br>
      **创建目录时默认权限（drwxrwxrwx）**, 减去-----w--w-, 剩下-rwxr-xr-x</br>
        因为文件本身没有执行权限，所以创建时默认没有x。计算时不要用数字直接刨去，要用去掉的权限来看。
      * 修改创建时的权限。`umask 002` 之后创建文件时同组也拥有和owner一样的权限，
   * 隐藏权限</br>
   使用 `chattr` 给文件设置隐藏权限，只能在Ext2/Ext3/Ext4的Linux文件系统上完整支持。</br>
   使用`charttr [+-=] [aAcCdDeFijPsStTu] file`给文件加上减去或者设置权限。详细属性设置使用 `man chattr` 
   * 特殊文件权限</br>
      + set UID</br>
       出现在文件拥有者的x位置上，如/usr/bin/passwd。修改密码是修改/etc/shadow，该文件只能root修改，其他用户可以改自己的密码。过程是这个样，其他用户有执行/usr/bin/passwd的权限，在用户执行修改密码命令时短暂获得root权限，修改完文件然后结束。要素是以下几项。
         - 执行者拥有x权限
         - SUID仅对二进制程序有效
         - 本权限仅限执行过程有效，且在期间获得root权限
      + set GID</BNR>
       当s出现在群组的x位置上时成为set GID。如/usr/bin/locate，去执行/var/lib/mlocate/mlocate.db。要素类似
         - SGID仅对二进制程序有效
         - 程序执行者需要有程序的x权限
         - 执行过程中获得程序的群组--slocate--的权限
         
         该权限也可以使用在文件夹上
      + Sticky Bit</br>
       只针对文件夹有效，对文件无效。在other的x上是一t。作用如下
         - 使用者对文件夹拥有wx权限，即写入权限
         - 使用者在该文件夹下创建文件夹和文件后，只有创建者和root能删除
       
        例如在/tmp就有该权限，所有人全权限。使用root创建一个文件，使用其他账号登入不能删除该文件
      + SUID/GUID/SBIT权限设置</br>
       SUID代表4，GUID代表2，SBIT代表1。使用数字设置权限时，把这个数字放在权限数字位置前面，有多个时累加。如将文件权限设置为'-rwsr-xr-x'时，这样更改权限 `chmod 4755 filename`。在设置7666时，会出现大写ST的情况如下
       ![fileST](./picture/fileST.png)</br>
       赋权时也可以使用字母SUID是u+s，SGID为g+s，SBIT是o+t。如：
         ```
         chmod u=rwxs,go=x test
         chmod g+s,o+t test
         ```
   * 查看文件类型使用 `file filename` 命令</br>
      如 `file /usr/bin/passwd`查询结果
      ```
      /usr/bin/passwd: setuid ELF 64-bit LSB pie executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-aarch64.so.1, BuildID[sha1]=49f22547cc152082477e4f2b00eb08056cdb29d9, for GNU/Linux 3.7.0, stripped
      ```
      
6. 文件内容浏览</br>
      浏览文件，几个常用命令</br>
      `cat` 从第一行开始显示</br>
      `tac` 从最后一行开始显示，和cat反着</br>
      `nl` 显示出来，顺便显示行号</br>
      `more` 一行一行显示</br>
      `less` **和more类似，但可以向上翻页** 比较重要的命令，man文档就是用的less命令</br>
      `head` 只看头几行</br>
      `tail` 只看尾巴几行</br>
      `od` 以二进制方式读取文件内容</br>

   * ` cat [-AbEnTv]` -n 可以打出行号
   * `tail -f filename` 连续输出
7. 修改文件时间或创建文件 `touch` </br>
   linux中每个文件会记录多个时间。其中有几个重要的时间查看</br>
      * modification time(mtime)。文件内容修改时记录。`ls -l filename`查看的时间就是该时间
      * status time(ctime)。状态修改时更新该事件。如权限和属性。`ls -l --time=ctime filename`
      * access time(atime)。文件被读取就会更新该事件。如被cat过。`ls -l --time=atime`

   如果文件时间错乱了，比如系统时间错误导致创建的时间在未来等问题时。可以使用touch命令修正, ``touch [-acdmt] filename``。
      * -a: 修改access time
      * -c: 修改mtime和atime, 没有文件则创建文件
      * -d: 后面可以接续自定的时间，而不是当前的时间，--date=
      * -m: 仅修改mtime
      * -t  ：后面可以接欲修订的时间而不用目前的时间，格式为\[YYYYMMDDhhmm\]
8. 指令和文件的搜寻
   [第六章最后一节]
---
# 四、Linux磁盘与文件系统
   
   

   



___

## 四、测试
* 这是谷歌[gooooooooooogle](https://www.google.com)
* 这不是我的邮箱<abc@hello.com>

	

	

	
	