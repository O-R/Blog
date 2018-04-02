# yum 源配置

在vm中安装了 `CentOS7` **minimal** ，既没有 `wget` 又没有 `yum` 源，很尴尬。
解决方法：
1. 配置静态IP
1. 使用阿里云Yum源，更改Yum仓库。

## 配置静态IP

> [root@localhost ~]# cd /etc/sysconfig/network-scripts/  
> [root@localhost network-scripts]# vim ifcfg-ens32  
> 
> ``` bash
> TYPE=Ethernet
> BOOTPROTO=static ##设置为静态IP模式
> NAME=ens32
> UUID=2626dc0c-2aa4-471f-81b4-794d83e727ac
> DEVICE=ens32
> ONBOOT=yes
> IPADDR=192.168.0.111  ## IP地址
> NETMASK=255.255.255.0 ## 必填
> GATEWAY=192.168.0.1   ## 网关
> DNS1=192.168.0.1      ## DNS解析
> ```
> 
> [root@localhost network-scripts]# systemctl restart network  
> [root@localhost network-scripts]# ping -c 4 www.baidu.com  
> PING www.a.shifen.com (163.177.151.110) 56(84) bytes of data.  
> 64 bytes from 163.177.151.110 (163.177.151.110): icmp_seq=1 ttl=55 time=6.72 ms  
> 64 bytes from 163.177.151.110 (163.177.151.110): icmp_seq=2 ttl=55 time=6.55 ms  
> 64 bytes from 163.177.151.110 (163.177.151.110): icmp_seq=3 ttl=55 time=6.66 ms  
> 64 bytes from 163.177.151.110 (163.177.151.110): icmp_seq=4 ttl=55 time=6.16 ms  
> 
> --- www.a.shifen.com ping statistics ---  
> 4 packets transmitted, 4 received, 0% packet loss, time 3006ms  
> rtt min/avg/max/mdev = 6.166/6.526/6.722/0.224 ms  

## 更改Yum仓库（有 `curl` ）

1. 使用 `curl http://mirrors.aliyun.com/repo/Centos-7.repo  ` 获取这个文件
1. 备份并用第一步的内容替换系统的repo文件
    > 
    > ```
    > [root@localhost ~]# cd /etc/yum.repos.d/
    > [root@localhost yum.repos.d]# cp CentOS-Base.repo CentOS-Base.repo.bak
    > [root@localhost yum.repos.d]# vim CentOS-Base.repo
    > ```
    > 
    > 按第一步的内容修改 CentOS-Base.repo
    
1. 执行yum源更新命令 
    > 
    > ```
    > yum clean all 
    > yum makecache 
    > yum update
    > ```

## 更改Yum仓库

1. 手动下载repo文件 http://mirrors.aliyun.com/repo/Centos-7.repo  
    获取到base 部分
    > ```
    > [base]
    > name=CentOS-$releasever - Base - mirrors.aliyun.com
    > failovermethod=priority
    > baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
    >         http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
    > #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os
    > gpgcheck=1
    > gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
    > ```

1. 备份并修改系统的repo文件
    > 
    > ```
    > [root@localhost ~]# cd /etc/yum.repos.d/
    > [root@localhost yum.repos.d]# cp CentOS-Base.repo CentOS-Base.repo.bak 
    > [root@localhost yum.repos.d]# ls
    > Centos-7.repo     CentOS-Base.repo.bak  CentOS-Debuginfo.repo  CentOS-Media.repo    CentOS-Vault.repo
    > CentOS-Base.repo  CentOS-CR.repo        CentOS-fasttrack.repo  CentOS-Sources.repo
    > [root@localhost yum.repos.d]# vim CentOS-Base.repo
    > ```
    > 
    > 按第一步的[base]修改[base]部分

1. 执行yum源更新命令 
    > 
    > ```
    > yum clean all 
    > yum makecache 
    > yum update
    > ```
1. 现在可以安装 `wget` 了
    > 
    > ```
    > [root@localhost ~]# cd /etc/yum.repos.d/
    > [root@localhost yum.repos.d]# yum install wget -y
    > [root@localhost yum.repos.d]# wget http://mirrors.aliyun.com/repo/Centos-7.repo
    > [root@localhost yum.repos.d]# cp Centos-7.repo CentOS-Base.repo
    > ```
    最后重复执行第三步。