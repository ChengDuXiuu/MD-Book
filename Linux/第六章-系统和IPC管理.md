## 系统管理

### 查询系统版本

* 查看Linux系统版本:

```bash 
$uname -a
$lsb_release -a
```

* 查看Unix系统版本：操作系统版本:

```bash 
$more /etc/release
```

### 查询硬件信息

* 查看CPU使用情况:

```bash
$sar -u 5 10
```

* 查询CPU信息:

```bash 
$cat /proc/cpuinfo
```

* 查看CPU的核的个数:

```bash
$cat /proc/cpuinfo | grep processor | wc -l
```

* 查看内存信息:

```bash
$cat /proc/meminfo
```

* 显示内存page大小（以KByte为单位）:

```bash
$pagesize
```

* 显示架构:

```bash
$arch
```

### 设置系统时间

* 显示当前系统时间:

```bash
$date
```

* 设置系统日期和时间(格式为2014-09-15 17:05:00):

```bash
$date -s 2014-09-15 17:05:00
$date -s 2014-09-15
$date -s 17:05:00
```

* 设置时区:

```bash
选择时区信息。命令为：tzselect
根据系统提示，选择相应的时区信息。
```

强制把系统时间写入CMOS（这样，重启后时间也正确了）:

```bash
$clock -w
```

警告

设置系统时间需要root用户权限.

格式化输出当前日期时间:

```bash
$date +%Y%m%d.%H%M%S
>20150512.173821
```

## IPC资源管理

### IPC资源查询

查看系统使用的IPC资源:

```bash
$ipcs

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status

------ Semaphore Arrays --------
key        semid      owner      perms      nsems
0x00000000 229376     weber      600        1

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages
```

查看系统使用的IPC共享内存资源:

```bash
$ipcs -m
```

查看系统使用的IPC队列资源:

```bash
$ipcs -q
```

查看系统使用的IPC信号量资源:

```bash
$ipcs -s
```

应用示例：查看IPC资源被谁占用

有个IPCKEY：51036 ，需要查询其是否被占用；

1. - 首先通过计算器将其转为十六进制:

		51036 -> c75c

2. 如果知道是被共享内存占用:

	```bash
	$ipcs -m | grep c75c
	0x0000c75c 40403197   tdea3    666        536870912  2
	```

3. 如果不确定，则直接查找:

	```bash
	$ipcs | grep c75c
	0x0000c75c 40403197   tdea3    666        536870912  2
	0x0000c75c 5079070    tdea3    666        4
	```

### 检测和设置系统资源限制

显示当前所有的系统资源limit 信息:

```bash
ulimit – a
```

对生成的 core 文件的大小不进行限制:

```bash
ulimit – c unlimited
```