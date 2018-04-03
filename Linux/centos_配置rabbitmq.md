# 配置rabbitmq

## `rabbitmq_management` (Web UI)

rabbitmq 提供了一个 Web UI 管理后台插件。可以用以下命令开启：
```
rabbitmq-plugins enable rabbitmq_management
```
访问网址是：http://server-name:15672/
rabbitmq 提供了一个默认管理员，账号：guest，密码：guest。此账号只能在局域网内访问：http://127.0.0.1:15672/

> **注意：** 版本在3.0之前端口是：55672  
> 更多说明参考：[Management Plugin](http://www.rabbitmq.com/management.html)

## 配置方式

1. 环境变量（Environment Variables）
    1. 优先从 `shell` 中获取系统环境变量
    1. 从配置环境获取， `Windows` 上是 `rabbitmq-env-conf.bat` , `Linux` 上是 `rabbitmq-env.conf`
1. 配置文件（Configuration File(s)）,某些配置没法采用 `sysctl format` （第二种方式）来配置的可以采用在 `advanced.config` 中配置。在RabbitMQ 3.7.0之前 `advanced.config` 是命名为 `rabbitmq.config` ，这是一个标准的 Erlang 配置文件。
    1. `advanced.config`，[advanced.config.example](https://github.com/rabbitmq/rabbitmq-server/blob/master/docs/advanced.config.example)
    1. `rabbitmq.conf`，[rabbitmq.conf.example](https://github.com/rabbitmq/rabbitmq-server/blob/master/docs/rabbitmq.conf.example)
1. `rabbitmqctl` 用于管理 virtual hosts, users and permissions.

`advanced.config`、`rabbitmq.conf` 和 `rabbitmq-env.conf` 默认位置

- **Generic UNIX** - $RABBITMQ_HOME/etc/rabbitmq/
- **Debian** - /etc/rabbitmq/
- **RPM** - /etc/rabbitmq/
- **Mac OSX (Homebrew)** - ${install_prefix}/etc/rabbitmq/, the Homebrew prefix is usually /usr/local
- **Windows** - %APPDATA%\RabbitMQ\

`advanced.config`、`rabbitmq.conf` 和 `rabbitmq-env.conf` 默认不存在，可以在以上位置创建。  

关于配置文件位置具体参考：[config-location](http://www.rabbitmq.com/configure.html#config-location)

## 环境变量的配置（简单介绍 `rabbitmq-env.conf` ）

- 说明

**rabbitmq-env.conf ** 包含了用于RabbitMQ环境变量的设置。RabbitMQ 在获取环境变量的时候首先从 `shell` 中获取，其次 `rabbitmq-env.conf` ，最后是启动脚本（startup script）中的默认值。只要前面获取到的值为空，则继续往后取值，否则取其值。

在配置文件 `rabbitmq-env.conf` 中，变量名会去掉前缀 `RABBITMQ_` , 如 `RABBITMQ_NODE_PORT` -> `NODE_PORT` 

- 举例（重写节点名称"rabbit" -> "hare"）

``` 
# I am a complete rabbitmq-env.conf file.
# Comment lines start with a hash character.
# This is a /bin/sh script file - use ordinary envt var syntax
NODENAME=hare
```

- [RabbitMQ 环境变量](http://www.rabbitmq.com/configure.html#define-environment-variables)

- [默认位置](http://www.rabbitmq.com/relocate.html)

Name  |  Location
-  |  -
RABBITMQ_BASE  |  (Not used - Windows only)
RABBITMQ_CONFIG_FILE  |  ${install_prefix}/etc/rabbitmq/rabbitmq
RABBITMQ_MNESIA_BASE  |  ${install_prefix}/var/lib/rabbitmq/mnesia
RABBITMQ_MNESIA_DIR  |  $RABBITMQ_MNESIA_BASE/$RABBITMQ_NODENAME
RABBITMQ_LOG_BASE  |  ${install_prefix}/var/log/rabbitmq
RABBITMQ_LOGS  |  $RABBITMQ_LOG_BASE/$RABBITMQ_NODENAME.log
RABBITMQ_SASL_LOGS  |  $RABBITMQ_LOG_BASE/$RABBITMQ_NODENAME-sasl.log
RABBITMQ_PLUGINS_DIR  |  /usr/lib/rabbitmq/plugins:$RABBITMQ_HOME/plugins <br> Note that /usr/lib/rabbitmq/plugins is used only when RabbitMQ is installed into the standard (default) location.
RABBITMQ_ENABLED_PLUGINS_FILE  |  ${install_prefix}/etc/rabbitmq/enabled_plugins
RABBITMQ_PID_FILE  |  $RABBITMQ_MNESIA_DIR.pid

## 配置 `rabbitmq.conf` 开启外网访问 **Web UI**

1. `firewall-cmd` 开启 15672 端口
    > ```
    > [root@localhost rabbitmq]# firewall-cmd --list-all
    > public (active)
    >   target: default
    >   icmp-block-inversion: no
    >   interfaces: ens32
    >   sources: 
    >   services: ssh dhcpv6-client
    >   ports: 
    >   protocols: 
    >   masquerade: no
    >   forward-ports: 
    >   source-ports: 
    >   icmp-blocks: 
    >   rich rules:    
	>
    > [root@localhost rabbitmq]# firewall-cmd --permanent --add-port=15672/tcp
    > success
    > [root@localhost rabbitmq]# firewall-cmd --reload
    > success
    > [root@localhost rabbitmq]# firewall-cmd --list-all
    > public (active)
    >   target: default
    >   icmp-block-inversion: no
    >   interfaces: ens32
    >   sources: 
    >   services: ssh dhcpv6-client
    >   ports: 15672/tcp
    >   protocols: 
    >   masquerade: no
    >   forward-ports: 
    >   source-ports: 
    >   icmp-blocks: 
    >   rich rules: 
    > ```
1. 取消注释  
    > 编辑 `rabbitmq.conf` ，取消注释 `loopback_users.guest = false`
    > ```
    > [root@localhost rabbitmq]# cd /etc/rabbitmq/
    > [root@localhost rabbitmq]# ls
    > enabled_plugins  rabbitmq.conf  rabbitmq.config.bak
    > [root@localhost rabbitmq]# vim rabbitmq.conf
    > ```
1. 重启
    > ```
    > /sbin/service rabbitmq-server stop
    > /sbin/service rabbitmq-server start
    > ```