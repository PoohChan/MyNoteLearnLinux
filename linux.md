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
       当s出现在群组的x位置上时成为set GID。如/usr/bin/locate拥有的权限，去执行/var/lib/mlocate/mlocate.db。该命令和文件都是同一群组，命令的group权限的x位置上是s</br>
       要素类似
         - SGID对二进制程序有效
         - 程序执行者需要有程序的x权限
         - 执行过程中获得程序的群组--slocate--的权限
         
         该权限也可以使用在文件夹上。文件夹group的x变为s，文件夹下群组用户创建的文件的群组为该群组。没有该权限的话，创建的文件的群组将是自己
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
   * `which` 搜寻命令。搜寻可执行文件, 参数-a查出所有而非第一个找到的。该命令根据PATH环境变量规范的路径查询可执行文件</br>
    `which ls` 查看ls命令的位置</br>
    `which which` 查看which命令的位置，结果有两条一个是别名</br>
    `which history` 查看history命令没有结果，因为history是bash内置命令，不在PATH内。</br>
    或者使用 `type` 命令
   * 文件的搜寻。**一般不用find命令**速度慢费硬盘。
      + `whereis [-bmsu]` 命令在特定的目录下搜寻
         - `-l` 列出whereis回去查询的主要目录
         - `-b` 只找寻二进制文件
         - `-m` 只找寻在manual路径下的文件
         - `-s` 只找寻source来源的文件
         - `-u` 只找寻不在上述三个项目中的特殊文件

         例如：`whereis ifconfig` 找寻ifconfig的位置。`whereis -m passwd`在手册中寻找passwd</br>
         快的原因是没有全盘搜索，只在特定的目录下进行搜寻， 使用`whereis -l` 便可知搜寻范围
      + `locate/updatedb`
         - locate \[-ir\] keyword
           - `-i` 忽略大小写的差异
           - `-c` 不输出文件名，只计算找到文件的数量
           - `-l` 控制结果输出几行
           - `-S` 输出locate所使用的数据库文件相关信息，包括数据库记录的文件目录数量等
           - `-r` 后面可以接正则表达式
       
           例如：`locate -l 6 passwd` 输出和passwd相关的5个文件名。第一次查询提示没有数据库，使用`updatedb`更新后再查询。</br>
           `locate -S` 查询数据库文件记录信息。</br>
           使用限制是文件名存储在/var/lib/mlocate数据库中的，没有记录的话及搜寻不到，数据库一般每天自动更新一次硬盘上的文件名。如果是新的文件名没有记录进去的话，使用`updatedb`命令进行更新，更新搜寻文件夹记录范围等使用的配置在/etc/updatedb.conf中记录。
      + `find [path] [option] [action]` 
        - 与时间有关的选项。有-atime, -ctime, -mtime。以-mtime说明：
          - -mtime n: n天前的一天之内被更改过的文件
          - -mtime +n: n天之前被更改过的文件
          - -mtime -n: n天之内被更改过的文件
          - -newer file: file作为一个存在的文件，列出比file更新的文件
        
          例如：`find / -mtime 0` 含义是24小时之内所有更新过的文件</br>
          `find /etc -newer /etc/passwd` 含义是在/etc寻找比/etc/passwd更新的文件</br>
          +n, -n, n 构成n天前，n天内，和n天那一天
        - 与用户和群组相关的参数
          - -uid n: n是使用者的uid
          - -gid n: n是群组名称的id
          - -user name: name是使用者的名称
          - -group name: name是群组的名称
          - -nouser: 寻找文件的用户不在/etc/passwd中的用户的文件
          - -nogroup: 寻找群组不在/etc/group的群组的文件
          
          例如：`find /home -user dmtsai` 在/home下寻找用户是dmtsai的文件</br>
          `find / -nousr` 在根目录下寻找没有没有在/etc/passwd中记录的用户的文件，一般使用源码编译时会有这种情况发生
        - 与文件权限和名称相关的参数
          - -name filename: 搜寻名称是filename的文件
          - -size [+-]SIZE: 搜寻比SIZE大还是小的文件，SIZE规格c代表Byte，k代表1024Bytes。如搜寻比50k大的文件“-size +50k”
          - -type TYPE    ：搜寻文件的类型为 TYPE 的，类型主要有：一般正规文件 （f）, 设备文件 （b, c）,
                   目录 （d）, 链接文件 （l）, socket （s）, 及 FIFO （p） 等属性。
          - -perm mode  ：搜寻文件权限“刚好等于” mode 的文件，这个 mode 为类似 chmod
                 的属性值，举例来说， -rwsr-xr-x 的属性为 4755 ！
          -  -perm -mode ：搜寻文件权限“必须要全部囊括 mode 的权限”的文件，举例来说，
                 我们要搜寻 -rwxr--r-- ，亦即 0744 的文件，使用 -perm -0744，
                 当一个文件的权限为 -rwsr-xr-x ，亦即 4755 时，也会被列出来，
                 因为 -rwsr-xr-x 的属性已经囊括了 -rwxr--r-- 的属性了。
          - -perm /mode ：搜寻文件权限“包含任一 mode 的权限”的文件，举例来说，我们搜寻
                 -rwxr-xr-x ，亦即 -perm /755 时，但一个文件属性为 -rw-------
                 也会被列出来，因为他有 -rw.... 的属性存在！

            例如：`find / -name passwd` 在根目录下寻找passwd的文件</br>
            `find / -size +1M` 找出系统中规格大于1M的文件</br>
            `find / -name "*passwd*"` 用关键字方式在根目录下像passwd的文件</br>
            `find /run -type s` 查找/run下类型是socket类型的文件</br>
            `find / -perm /7000` 7000 就是 ---s--s--t

            使用-perm就能找到一些特殊权限的文件，还可以把待搜索文件夹并列写出来。例如在/usr/bin, /usr/sbin下寻找有SUID, SGID权限的文件：</br>
            `find /usr/bin /usr/sbin -perm /6000`
         - 额外的可进行的操作
           - -exec command: command为其他指令， -exec后可接续额外的指令处理搜寻到结果
           - -print: 将结果打印出来，这个动作是默认动作
           
           例如：`find /usr/bin /usr/sbin -perm /7000 -exec ls -l {} \;` 该命令将上个例子中的文件信息具体打印出来，有几个特殊的地方“{}”, “\\;”，“-exec”.
             -  {} 代表的是find找到的内容，在命令中前一半由find找到的内容放到{}中
             -  从-exec 到 \;。代表额外动作从 `-exec` 开始到 `\;` 结束，即`ls -l {}` 注意 `ll` 命令是别名不能放到其中，其他很多场合也是这样
             -  `\;` 因为 `;` 在bash环境下有特殊意义，因此使用反斜杠来跳脱

   一般使用whereis和locate就够了，在这两个命令不能满足时再使用find命令。find命令比较费硬盘

   建立新group, `groupadd project`</br>
   建立新用户, `useradd -G project alex`</br>
   `useradd -G project arod`</br>
   创建文件夹`mkdir /srv/ahome`</br>
   更改群组`chgrp project /srv/ahome`</br>
   更改权限`chomd 770 /srv/ahome`</br>
   `passwd alex XshCh&xHyi7pybCL`</br>
   `passwd arod f%n1s483Ke&w#oEm`</br>
   `su - alex` 切换的alex账户</br>
   `cd /srv/ahome`</br>
   `touch 1234` 创建文件</br>
   `su - arod` 切换用户</br>
   `cd /srv/ahome` </br>
   `ls -l` 创建的1234文件不能编辑，文件的群组是alex，不是我们想要的效果</br>
   `su` 切换到root，重新分配权限</br>
   `chmod 2770 /srv/ahome` 将文件夹建立成共享状态，建立的文件的群组是创建者的群组

## 四、磁盘与文件系统管理
最传统的磁盘文件系统使用EXT2。复习磁盘分区。重点是inode, block和superblock
1. 磁盘组成分区复习
   * 磁盘由圆形盘片，机械臂，主轴马达。马达转动盘片，使得机械臂读取盘片上的数据
   * 盘片组成
     * 扇区。有512Bytes和4K两种规格
     * 柱面。由扇区组成的圆环就是一个柱面
     * 早期分区以柱面为最小单位，现在以扇区为最小单位
     * 磁盘分区表两种格式，早期的MBR, 和GPT分区表
     * MBR分区表中，第一个扇面最重要。分为主要开机区(Master boot record MBR)和分区表(partition table)，其中MBR占有446Bytes，partition占有64Bytes
     * GPT分区数量扩充，支持容量超过2TB
     * 磁盘命名方式：
       + /dev/sd[a-p][1-128]：为实体磁盘的磁盘文件名
       + /dev/vd[a-d][1-128]：为虚拟磁盘的磁盘文件名
2. 文件系统特性






---
# 四、Linux磁盘与文件系统
   
   

   



___

## 四、测试
* 这是谷歌[gooooooooooogle](https://www.google.com)
* 这不是我的邮箱<abc@hello.com>

	

	

	
	