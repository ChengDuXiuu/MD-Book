`引言`

命令格式 ：命令 [-选项] [参数] 

例 ： ls -la /etc 

说明：1）个别命令使用不遵循此格式

   	 2）当有多个选项时，可以写在一起
   	
   	 3）简化选项与完整选项-a 等于 --all





## 目录内容显示ls

命令英文原意：list 

命令所在路径：/bin/ls 

执行权限：所有用户

功能描述：显示目录文件

语法：ls  选项[-ald]  [文件或目录] 

​			-a    显示所有文件，包括隐藏文件

​			-l     详细信息显示

​		   -d    查看目录属性，而不看目录下文件信息

​			-h    人性化显示文件容量

​			-i     显示文件索引



## 目录处理

### mkdir

命令英文原意：make directories 

命令所在路径：/bin/mkdir 

执行权限：所有用户

语法：mkdir -p  [目录名] 

功能描述：创建新目录-p  递归创建

范例： $ mkdir -p /tmp/Japan/boduo 

 	  $ mkdir /tmp/Japan/longze /tmp/Japan/cangjing



### cd

命令英文原意：change directory 

命令所在路径：shell内置

命令执行权限：所有用户

语法：cd [目录] 

功能描述：切换目录

范例： $ cd  /tmp/Japan/boduo     切换到指定目录

 	  $ cd  ..        回到上一级目录





### pwd 

命令英文原意： print working directory 

命令所在路径：/bin/pwd 

执行权限：所有用户

语法：pwd 

功能描述：显示当前目录

范例：$ pwd 

  /tmp/Japan



### rmdir

命令英文原意：remove empty directories 

命令所在路径：/bin/rmdir 

执行权限：所有用户

语法：rmdir [目录名] 

功能描述： 删除空目录

范例： $ rmdir /tmp/Japan/boduo





### cp

命令英文原意：copy 

命令所在路径：/bin/cp 

执行权限：所有用户

语法：cp  -rp  [原文件或目录] [目标目录] 

-r  复制目录

-p  保留文件属性，即当cp文件时，相当于新建了一个文件，其日期将会改变，如果想保留之前文件得时间，则使用这个属性

功能描述：复制文件或目录

范例：$ cp  -r /tmp/Japan/cangjing  /root 

将目录/tmp/Japan/cangjing复制到目录/root下

`复制多个文件或目录`

$ cp  -rp /tmp/Japan/boduo /tmp/Japan/longze /root 

将/tmp/Japan目录下的boduo和longze目录复制到/root下，保持目录属性

`复制目录并修改名称`

$ cp  -r /tmp/Japan/boduo /root/update

将  /tmp/Japan/boduo 目录复制到root目录下并修改名为update





### mv

命令英文原意：move 

命令所在路径：/bin/mv 

执行权限：所有用户

语法：mv  [原文件或目录]   [目标目录] 

功能描述：剪切文件、改名





### rm

命令英文原意：remove 

命令所在路径：/bin/rm 

执行权限：所有用户

语法：rm  -rf   [文件或目录] 

-r  删除目录

-f  强制执行

功能描述：删除文件

范例：$ rm  /tmp/yum.log 

删除文件/tmp/yum.log 

`删除目录及其下面所有的子目录和文件`

$ rm -rf  /tmp/Japan/longze 



## 文件处理命令

### touch

命令所在路径：/bin/touch 

执行权限：所有用户 

语法：touch  [文件名]                 

功能描述：创建空文件 

范例： $ touch Japanlovestory.list

`创建多个文件`

  $ touch Japanlovestory.list   a.txt    b.txt

### cat

命令所在路径：/bin/cat 

执行权限：所有用户 

语法：cat [文件名] 

功能描述：显示文件内容            

   -n  显示行号 

范例： $ cat  /etc/issue              $ cat  -n /etc/services

> 不太适合浏览内容很长的文件，

### more

命令所在路径：/bin/more 

执行权限：所有用户 

语法：more  [文件名]                  

   (空格) 或f       翻页               

   (Enter)             换行                 

  q或Q              退出 

功能描述：分页显示文件内容 

范例：   $ more  /etc/services

> 空格  ： 一页一页翻     回车 ：一行一行    q ： 退出
>
> 不能向上翻页

### less

命令所在路径：/usr/bin/less 

执行权限：所有用户 

语法：less  [文件名] 

功能描述：分页显示文件内容（可向上翻页） 

范例：   $  less  /etc/services

**tips: 按下 / 后可以搜索 会反显高亮    按n向下查找按q退出（more也可以）**

> 空格  ： 一页一页下翻                      回车 ：一行一行                   q ： 退出                       page up 一页一页上翻                 up键：一行一行上翻

### head

 	命令所在路径：/usr/bin/head 

执行权限：所有用户 

语法：head  [文件名] 

功能描述：显示文件前面几行    

-n 指定行数 

范例：   $ head -n 20 /etc/services



### tail

命令所在路径：/usr/bin/tail 

执行权限：所有用户 

语法：tail  [文件名] 

功能描述：动态显示文件内容  

​    -n 指定行数    

   -f  动态显示文件末尾内容 

范例：   $ tail -n 18 /etc/services

> 并不会返回到命令行，例如我查看日志文件，而这个日志文件实时更新的，那么就可以使用这个命令进行监控





## 链接命令

### ln

命令英文原意：link 

命令所在路径：/bin/ln 

执行权限：所有用户 

语法：ln  -s  [原文件]  [目标文件]                 

 -s  创建软链接 

功能描述：生成链接文件



范例：        

$ ln -s  /etc/issue  /tmp/issue.soft        

 创建文件/etc/issue的软链接/tmp/issue.soft        

$ ln  /etc/issue  /tmp/issue.hard        

 创建文件/etc/issue的硬链接/tmp/issue.hard



### 软链接特征：

类似Windows快捷方式 

1、lrwxrwxrwx    l 软链接 

软链接文件权限都为rwxrwxrwx ，并不能决定源文件的权限，即源文件权限该怎样还怎样

2、文件大小-只是符号链接 

3、/tmp/issue.soft -> /etc/issue 箭头指向原文件

4、源文件删除后 软链接不能使用



### 硬链接特征：

1、拷贝cp -p + 同步更新 ，和源文件一模一样 

echo "this is a test" >> /etc/motd 

2、可通过i节点识别 

3、不能跨分区

4、不能针对目录使用

5、源文件删除后不影响硬链接





## 权限管理命令

### chmod 

命令英文原意：change the permissions mode of a file 

命令所在路径：/bin/chmod 

执行权限：所有用户 

语法：

* chmod  [{ugoa}{+-=}{rwx}] [文件或目录]      

	> u所有者，g用户组，o其他人，a所有人      ==》   +赋予   -删除   =覆写   ，即给什么类型的用户 赋予或是删除 什么样的权限   

	![image-20210716221727831](第二章-Linux常用命令.assets/image-20210716221727831.png)

	> 如果需要批量操作用户类型，则  chmod u-x,o+w,g+x demo.txt

	​               

  [mode=421 ]  [文件或目录]                          

 -R  递归修改 

功能描述：改变文件或目录权限



权限的数字表示      

r  ---- 4      w ---- 2      x  ---- 1

rwxrw-r--    7   6    4



范例：       

 $ chmod  g+w  testfile         

赋予文件testfile所属组写权限      

`递归修改目录下所有文件`

  $ chmod  -R 777  testdir         

修改目录testfile及其目录下文件为所有用户具有全部权限





| 代表字符 | 权限     | 对文件的含义     | 对目录的含义                   |
| -------- | -------- | ---------------- | ------------------------------ |
| r        | 读权限   | 可以查看文件内容 | **可以列出目录中的内容**       |
| w        | 写权限   | 可以修改文件内容 | **可以在目录中创建、删除文件** |
| x        | 执行权限 | 可以执行文件     | **可以进入目录**               |







tips:

**1.rwx作用在文件时**

1> r:read，可以读取，查看

2> w:writte, 可以修改，但是不代表可以删除该文件，删除一个文件的前提是对该文件所在的目录（文件夹）具有写的权限，才能删该文件

3> x:execute，可以被执行

**2.rwx作用在目录时**

1> r，ls查看目录内容

2> w, 可以修改，目录内创建+删除+重命名目录

3> x, 可以进入该目录 



### 命令名称：chown

命令英文原意：change file ownership

命令所在路径：/bin/chown 

执行权限：root 

语法：chown  [用户] [文件或目录]  

功能描述：改变文件或目录的所有者 

范例：$ chown  shenchao fengjie               

 改变文件fengjie的所有者为shenchao



### 命令名称：chgrp

命令英文原意：change file group ownership 

命令所在路径：/bin/chgrp

执行权限：所有用户 

语法：chgrp  [用户组]  [文件或目录]  

功能描述：改变文件或目录的所属组

范例：$ chgrp lampbrother fengjie            

​    改变文件fengjie的所属组为lampbrother



### 命令名称：umask

命令英文原意：the user file-creation mask 

命令所在路径：Shell内置命令 

执行权限：所有用户 

语法：umask [-S]             

-S   以rwx形式显示新建文件缺省权限 

功能描述：显示、设置文件的缺省权限 

范例： $ umask -S

tips: 新建文件是默认没有x权限（比如：防止木马病毒攻击）





## 文件搜索命令

### find 

命令所在路径：/bin/find 

执行权限：所有用户 

语法：find  [搜索范围]  [匹配条件] 

功能描述：文件搜索

参数：

* -type 根据文件类型查找        ===》   f 文件    d 目录    l 软链

* -inum  根据i节点查找   即每个文件都有的索引
* -name  根据文件名
* -size  根据文件大小
* -user  根据所有者查询
* -exec  关联查询

范例：

**根据文件名搜索**

* $ find  /etc  -name  init           

	在目录/etc中查找文件init       

	>  -iname 不区分大小写       

* $ find /etc -name \*init\*

	在目录/etc中查找文件名包含init     

* $ find /etc -name init??？

	在目录/etc中查找文件名init开头并有3个字符的   

*   find /etc -name init*

	在目录/etc中查找文件名init开头的   

> tips:      \* 匹配任意字符，?匹配单个字符*

**根据文件大小搜索**

* $ find  /  -size  +204800           

	在根目录下查找大于100MB的文件        

	>  +n  大于        -n 小于        n 等于        

* $ find  /etc -size  +163840 -a -size  -204800          

	在/etc下查找大于80MB小于100MB的文件          

	> -a 两个条件同时满足         -o 两个条件满足任意一个即可     

**根据所有者搜索**

* $ find  /home  -user  shenchao         

	在根目录下查找所有者为shenchao的文件       

	> -group  根据所属组查找

**根据文件属性查询**

* $ find  /etc  -cmin  -5         

	在/etc下查找5分钟内被修改过属性的文件和目录

​       -amin  访问时间 access   

​       -cmin  文件属性 change     

​		-mmin 文件内容 modify

**根据文件索引查询**

![image-20210717143301716](第二章-Linux常用命令.assets/image-20210717143301716.png)

   

**关联搜索**

* $ find /etc -name inittab -exec ls -l {} \;         

	在/etc下查找inittab文件并显示其详细信息          

	> -exec -ok 命令 {} \;  对搜索结果执行操作
	>
	> tips:  -exec直接执行，ok会询问操作





### locate 

命令所在路径：/usr/bin/locate 

执行权限：所有用户 

语法：locate  文件名 

功能描述：在文件资料库中查找文件 ,相比于find 遍历查询，它建立了文件和索引关系，直接查询索引库即可，但是并不能保证文件是最新的

范例：$ locate inittab

`tips:新建文件locate不到，可以updatedb,更新文件资料库后查找[tmp不在文件资料库内],但是/tmp下文件并不会保存在资料库中`

**不区分大小写**

$  locate -i masichun.json           // -i 作用你不区分大小写类似 -iname



### which 

命令所在路径：/usr/bin/which

执行权限：所有用户 

语法：which  命令 

功能描述：搜索命令所在目录及别名信息 

范例：$ which ls



### whereis 

命令所在路径：/usr/bin/whereis 

执行权限：所有用户 

语法：whereis  [命令名称]  

功能描述：搜索命令所在目录及帮助文档路径 

范例：$ whereis ls



#### grep

>   搜索文件中有关键字的行，有就显示出

命令所在路径：/bin/grep 

执行权限：所有用户 

`格式`

```bash
grep [选项] "搜索内容" 文件名
```

`案例`

```bash
grep "root" /etc/passwd
```

![image-20210720142143681](第七章-Shell.assets/image-20210720142143681.png)

`选项：  `

*   -i：  忽略大小写  

*   -n：  输出行号  

*   -v：  反向查找  

*   --color=auto 搜索出的关键字用颜色显示

## 帮助命令

### 命令名称：man

命令英文原意：manual 

命令所在路径：/usr/bin/man 

执行权限：所有用户 

语法：man  [命令或配置文件] 

  (空格) 或f       翻页               

  (Enter)             换行                 

  q或Q              退出 

功能描述：获得帮助信息

范例： $ man ls                

查看ls命令的帮助信息             

 $ man services                

查看配置文件services的帮助信息

​                 ![img](第二章-Linux常用命令.assets/0-20210717150231353.png)        

1 命令的帮助  5配置文件的帮助

man 5 passwd -> 查看配置文件的帮助



### whatis

功能描述：获得命令的简单介绍信息

范例：whatis ls

​                 ![img](第二章-Linux常用命令.assets/0.png)        

类似的：apropos

语法：apropos 配置文件

whatis 类似于精确查找

apropos 模糊查找关键字 [功能等于 man -k ][可以tab快速补全]



### 命令名称：help

命令所在路径：Shell内置命令 

执行权限：所有用户 

语法：help 命令 

功能描述：获得Shell内置命令的帮助信息 

范例： $ help umask                

查看umask命令的帮助信息



## 用户管理命令

### useradd

命令所在路径：/usr/sbin/useradd 

执行权限：root 

语法：useradd 用户名 

功能描述：添加新用户 

范例： $ useradd yangmi



### 命令名称：passwd

命令所在路径：/usr/bin/passwd 

执行权限：所有用户 

语法：passwd 用户名 

功能描述：设置用户

范例： $ passwd yangmi

普通用户只能更改自己密码，root用户可以更改所有人密码





### 命令名称：who 

命令所在路径：/usr/bin/who 

执行权限：所有用户 

语法：who 

功能描述：查看登录用户信息 

范例： $ who

​                 ![img](第二章-Linux常用命令.assets/0-20210717151310656.png)        

参数分别为：

用户名  登录终端[tty本地终端/pts远程终端]  登录时间 IP地址[没有表示本地登录]



### 命令名称：w

命令所在路径：/usr/bin/w 

执行权限：所有用户 

语法：w 

功能描述：查看登录用户详细信息 

范例： $ w

![image-20210717152348033](第二章-Linux常用命令.assets/image-20210717152348033.png)

* up 9:18  连续登陆时间
* 4 users    四个用户
* load average :xxx   服务器负载情况  超过1.0  则服务器负载严重
* 四个用户详细信息





## 压缩解压命令

### 命令名称：gzip

命令英文原意：GNU zip 

命令所在路径：/bin/gzip 

执行权限：所有用户 

语法：gzip [文件]  

功能描述：压缩文件 ，不能压缩目录，并且压缩后源文件删除

压缩后文件格式：.gz



### 命令名称：gunzip  [= gzip -d]

命令英文原意：GNU unzip 

命令所在路径：/bin/gunzip 

执行权限：所有用户 

语法：gunzip [压缩文件] 

功能描述：解压缩.gz的压缩文件 

范例： $ gunzip boduo.gz

tips:只能压缩文件不能压缩目录



### 命令名称：tar

命令所在路径：/bin/tar 

执行权限：所有用户 

语法：tar  选项[-zcf] [压缩后文件名] [目录]                                   

-c    打包                 

-v    显示详细信息                 

-f     指定文件名            

-z     打包同时压缩 

功能描述：打包目录 压缩后文件格式：.tar.gz

范例：

**打包并压缩**

$ tar  -zcf   Japan.tar.gz  Japan        

将目录Japan打包并压缩为.tar.gz文件



**tar命令解压缩语法：**           

-x     解包           

-v    显示详细信息           

-f     指定解压文件           

-z     解压缩 

范例：$ tar  -zxvf  Japan.tar.gz



### 命令名称：zip

命令所在路径：/usr/bin/zip 

执行权限：所有用户 

语法：     

zip  选项[-r]  [压缩后文件名]  [文件或目录]            

-r    压缩目录 

功能描述：压缩文件或目录 

压缩后文件格式：.zip

范例：          

$ zip  buduo.zip  boduo             

压缩文件          

$ zip  -r  Japan.zip  Japan             

压缩目录

tips：会保留原文件

**命令名称：****unzip** 

命令所在路径：/usr/bin/unzip 

执行权限：所有用户 

语法：unzip  [压缩文件] 

功能描述：解压.zip的压缩文件 

范例：$ unzip test.zip



### 命令名称：bzip2 

命令所在路径：/usr/bin/bzip2 

执行权限：所有用户 

语法： bzip2  选项 [-k] [文件]                        

   -k   产生压缩文件后保留原文件 

功能描述：压缩文件 相较zip命令，压缩比惊人

压缩后文件格式：.bz2 

范例：$ bzip2 -k boduo               

 $ tar -cjf  Japan.tar.bz2 Japan



### 命令名称：bunzip2

命令所在路径：/usr/bin/bunzip2 

执行权限：所有用户 

语法： bunzip2  选项 [-k] [压缩文件]                               

-k   解压缩后保留原文件 

功能描述：解压缩 

范例：$ bunzip2  -k boduo.bz2           

   $ tar -xjf  Japan.tar.bz2



## 网络命令

### 指令名称：write

指令所在路径：/usr/bin/write 

执行权限：所有用户 

语法：write  <用户名>    

功能描述：给用户发信息，以Ctrl+D保存结束 

范例：   # write linzhiling

**tips:输入错误，control+delete 删除**



### 指令名称：wall 

命令英文原意：write all 

指令所在路径：/usr/bin/wall 

执行权限：所有用户 

语法：wall  [message]   

功能描述：发广播信息 

范例：   # wall  ShenChao is a honest man!



### 命令名称：ping 

命令所在路径：/bin/ping 

执行权限：所有用户 

语法：ping  选项  IP地址                      

 -c  指定发送次数 

功能描述：测试网络连通性 

范例：  #  ping 192.168.1.156



### 命令名称：ifconfig

命令英文原意：inter

ce configure 

命令所在路径：/sbin/ifconfig 

执行权限：root 

语法：ifconfig  网卡名称  IP地址 

功能描述：查看和设置网卡信息 

范例：# ifconfig  eth0 192.168.8.250



### 命令名称：mail

命令所在路径：/bin/mail 

执行权限：所有用户 

语法：mail [用户名] 

功能描述：查看发送电子邮件 

范例：# mail root



### 命令名称：last

命令所在路径：/usr/bin/last 

执行权限：所有用户 

语法：last 

功能描述：列出目前与过去登入系统的用户信息 

范例：# last



### 命令名称：lastlog

命令所在路径：/usr/bin/lastlog 

执行权限：所有用户 

语法：lastlog 

功能描述：检查某特定用户上次登录的时间 

范例：# lastlog             

\# lastlog -u 502



### 命令名称：traceroute 

命令所在路径：/bin/traceroute 

执行权限：所有用户 

语法：traceroute 

功能描述：显示数据包到主机间的路径 

范例：# traceroute www.lampbrother.net

![image-20210717161408216](第二章-Linux常用命令.assets/image-20210717161408216.png)





### 命令名称：netstat 

命令所在路径：/bin/netstat 

执行权限：所有用户 

语法：netstat  [选项] 

功能描述：显示网络相关信息

选项： 

​    -t  ： TCP协议  

   -u ： UDP协议  

   -l ： 监听  

   -r ： 路由  

  -n ： 显示IP地址和端口号 

范例：  

\#  netstat -tlun  查看本机启用的端口 

\#  netstat -an  查看本机所有的网络连接  

\#  netstat -rn  查看本机路由表



查看某一端口是否被占用：netstat -tunlp |grep 2181





### 命令名称：setup

命令所在路径：/usr/bin/setup 

执行权限：root 

语法：setup 

功能描述：配置网络 ，永久生效

范例：# setup



### 命令名称：mount

命令位置：/bin/mount 

执行权限：所有用户 

命令语法：mount [-t 文件系统] 设备文件名 挂载点 

范例：# mount -t iso9660 /dev/sr0 /mnt/cdrom





## 挂机重启命令

### shutdown命令

shutdown [选项] 时间  

选项：  -c：取消前一个关机命令               -h：关机                -r： 重启              -n：马上关机

> 挂机重启时会保存服务状态

### 其他关机命令

[root@localhost ~]# halt 

[root@localhost ~]# poweroff 

[root@localhost ~]# init 0



### 其他重启命令

[root@localhost ~]# reboot  

[root@localhost ~]# init 6





## 系统运行级别

0   关机 

1  单用户 

2  不完全多用户，不含NFS服务 

3  完全多用户

4  未分配 

5  图形界面

6  重启



[root@localhost ~]# cat /etc/inittab  

\#修改系统默认运行级别 

id:3:initdefault: 

[root@localhost ~]# runlevel 

\#查询系统运行级别