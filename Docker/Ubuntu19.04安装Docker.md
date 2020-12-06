# step 1: 安装必要的一些系统工具

> sudo apt-get update
> sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common

# step 2: 安装GPG证书

> curl -fsSL [http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg](https://links.jianshu.com/go?to=http%3A%2F%2Fmirrors.aliyun.com%2Fdocker-ce%2Flinux%2Fubuntu%2Fgpg) | sudo apt-key add -

# Step 3: 写入软件源信息

> sudo add-apt-repository "deb [arch=amd64] [http://mirrors.aliyun.com/docker-ce/linux/ubuntu](https://links.jianshu.com/go?to=http%3A%2F%2Fmirrors.aliyun.com%2Fdocker-ce%2Flinux%2Fubuntu) $(lsb_release -cs) stable"

# Step 4: 更新并安装 Docker-CE

> sudo apt-get -y update
> sudo apt-get -y install docker-ce

报错：ubuntu apt-get install 时报错curl : Depends: libcurl4 (= 7.58.0-2ubuntu3.6) but 7.61.0-1ubuntu2 is to b...

ubuntu apt-get install 时报错：Depends: ***(=某版本)but\***(另一版本)is to be installed

这时候就把这个***给purge后再重新装就好了

比如:

apt-get purge libcurl4

apt-get install curl

或者:

apt-get purge vim-common

apt-get install vim

## Step 5: 换源
 ![cd /etc/docker](https://math.jianshu.com/math?formula=cd%20%2Fetc%2Fdocker) 

cat daemon.json
 {
 "registry-mirrors": [
 "[https://kfwkfulq.mirror.aliyuncs.com](https://links.jianshu.com/go?to=https%3A%2F%2Fkfwkfulq.mirror.aliyuncs.com)",
 "[https://2lqq34jg.mirror.aliyuncs.com](https://links.jianshu.com/go?to=https%3A%2F%2F2lqq34jg.mirror.aliyuncs.com)",
 "[https://pee6w651.mirror.aliyuncs.com](https://links.jianshu.com/go?to=https%3A%2F%2Fpee6w651.mirror.aliyuncs.com)",
 "[https://registry.docker-cn.com](https://links.jianshu.com/go?to=https%3A%2F%2Fregistry.docker-cn.com)",
 "[http://hub-mirror.c.163.com](https://links.jianshu.com/go?to=http%3A%2F%2Fhub-mirror.c.163.com)"
 ],
 "dns": ["8.8.8.8","8.8.4.4"]
 }
 然后重启docker

> systemctl restart docker

![1607242242856](C:\Users\gao17\AppData\Roaming\Typora\typora-user-images\1607242242856.png)