## 更换源

* 进入容器系统

	```bash
	docker exec -it 容器ID /bin/bash
	```

* 查看系统信息

	```bash
	cat /etc/issue  或者
	cat /etc/os-release
	```

* 我的系统信息

	```bash
	# 如果和我系统信息不一致不敢保证软件源是否有冲突
	#cat /etc/issue
	Debian GNU/Linux 10 \n \l 
	
	#cat /etc/os-release
	PRETTY_NAME="Debian GNU/Linux 10 (buster)"
	NAME="Debian GNU/Linux"
	VERSION_ID="10"
	VERSION="10 (buster)"
	VERSION_CODENAME=buster
	ID=debian
	HOME_URL="https://www.debian.org/"
	SUPPORT_URL="https://www.debian.org/support"
	BUG_REPORT_URL="https://bugs.debian.org/"
	
	```

* 替换软件源:最好先备份:see_no_evil:

	```bash
	echo -e "deb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster main contrib non-free\ndeb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-updates main contrib non-free\ndeb https://mirrors.tuna.tsinghua.edu.cn/debian/ buster-backports main contrib non-free\ndeb https://mirrors.tuna.tsinghua.edu.cn/debian-security buster/updates main contrib non-free" > /etc/apt/sources.list
	```



## 安装vim

* Apt-get update
* Apt-get install vim -y

