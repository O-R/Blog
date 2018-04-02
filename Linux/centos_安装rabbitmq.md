# 安装`rabbitmq`

## 安装 `erlang`

`rabbitmq` 安装 依赖于 `erlang` ，所以要先安装 `erlang` 。  
`erlang` 有多种版本，这里采用 `rabbitmq` 提供的 `erlang-rpm` (Zero-dependency Erlang RPM for RabbitMQ)

1. 添加 `.repo` 配置文件 
    > 
    > ```
    > [root@localhost ~]# cd /etc/yum.repos.d/
    > [root@localhost yum.repos.d]# vi rabbitmq-erlang.repo
    > # In /etc/yum.repos.d/rabbitmq-erlang.repo
    > [rabbitmq-erlang]
    > name=rabbitmq-erlang
    > baseurl=https://dl.bintray.com/rabbitmq/rpm/erlang/20/el/7
    > gpgcheck=1
    > gpgkey=https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc
    > repo_gpgcheck=0
    > enabled=1
    > ```
    > 
1. 安装 `erlang`
    > ```
    > [root@localhost yum.repos.d]# yum install erlang -y
    > ```
    > 
1. 测试 `erlang` 是否安装成功
    > ```
    > [root@localhost yum.repos.d]# erl -v
    > Erlang/OTP 20 [erts-9.3] [source] [64-bit] [smp:1:1] [ds:1:1:10] [async-threads:10] [hipe] >  [kernel-poll:false]
    > 
    > Eshell V9.3  (abort with ^G)
    > 1> 
    > ```

## 安装 `RabbitMQ Server`

1. 下载 `rabbitmq-server-3.7.4-1.el7.noarch.rpm`
    > ```
    > [root@localhost yum.repos.d]# wget  > https://dl.bintray.com/rabbitmq/all/rabbitmq-server/3.7.4/rabbitmq-server-3.7.4-1.el7. noarch.rpm
    > ```
1. 安装
    > ```
    > [root@localhost yum.repos.d]# rpm --import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
    > [root@localhost yum.repos.d]# yum install rabbitmq-server-3.7.4-1.el7.noarch.rpm -y
    > ```
1. 启动服务
    > - 开机自启动
    > ```
    > chkconfig rabbitmq-server on 
    > ```
    > - 开启与停止
    > ```
    > /sbin/service rabbitmq-server start
    > /sbin/service rabbitmq-server stop
    > ```
