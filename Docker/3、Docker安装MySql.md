
## 安装 `mysql` 镜像

1.  拉取mysql 镜像：

    > ```
    > docker pull mysql/mysql-server
    > ```

1.  安装：

    > ```
    > docker run -d -p 3306:3306 --name [mysql 容器名称] mysql/mysql-server
    > ```
 
1.  查看日志：

    > ```
    > docker logs [mysql 容器名称]
    > docker logs [mysql 容器名称] 2>&1
    > docker logs [mysql 容器名称] 2>&1 | grep GENERATED （查看随机密码）
    > ```

1.  监视：

    > ```
    > docker inspect [mysql 容器名称]
    > ``

## mysql镜像

1.  进入mysql bash环境命令：

    > ```
    > docker exec -it [mysql 容器名称] bash
    > ```
 
1.  mysql登录命令：

    > ```
    > mysql -hIP -uUSENME -pPWD -PPORT DBNAME -e "sql"
    > ```
    > 
    > ```
    > mysql -uroot -p
    > ```
    > 
    > 通过 `docker logs [mysql 容器名称]` 查看生成的root账户随机密码
 
1.  mysql 设置：

    > -   使用mysql系统数据库
    >     ```
    >     use mysql
    >     ```
    > 
    > 
    > -   重置root账户密码
    > 
    >     ```
    >     SET PASSWORD FOR 'root'@'localhost' = PASSWORD('pwd123456');
    >     ```
    >
    > -   出现错误，数据库连接失败：
    > 
    >     Host '192.168.0.106' is not allowed to connect to this MySQL server
    > 
    >     远程连接失败是因为root用户host限制为localhost：
    > 
    >     ```
    >     mysql> select user,host from user;
    >     +---------------+-----------+
    >     | user          | host      |
    >     +---------------+-----------+
    >     | healthchecker | localhost |
    >     | mysql.session | localhost |
    >     | mysql.sys     | localhost |
    >     | root          | localhost |
    >     +---------------+-----------+
    >     4 rows in set (0.00 sec)
    >     ```
    > 
    > -   创建新用户
    > 
    >     ```
    >     mysql> CREATE USER 'or'@'localhost' IDENTIFIED BY 'pwd123456';
    >     mysql> GRANT ALL PRIVILEGES ON *.* TO 'or'@'localhost'
    >         ->     WITH GRANT OPTION;
    >     mysql> CREATE USER 'or'@'%' IDENTIFIED BY 'pwd123456';
    >     mysql> GRANT ALL PRIVILEGES ON *.* TO 'or'@'%'
    >         ->     WITH GRANT OPTION;
    >     ```
    > 
    >     ```
    >     mysql> select user,host from user
    >         -> ;
    >     +---------------+-----------+
    >     | user          | host      |
    >     +---------------+-----------+
    >     | or            | %         |
    >     | healthchecker | localhost |
    >     | mysql.session | localhost |
    >     | mysql.sys     | localhost |
    >     | or            | localhost |
    >     | root          | localhost |
    >     +---------------+-----------+
    >     6 rows in set (0.00 sec)
    >     ``` 
    >
    > -   字符集：
    >
    >     ```
    >     mysql> show variables like '%char%';
    >     +--------------------------+----------------------------+
    >     | Variable_name            | Value                       |
    >     +--------------------------+----------------------------+
    >     | character_set_client     | latin1                      |
    >     | character_set_connection | latin1                      |
    >     | character_set_database   | latin1                      |
    >     | character_set_filesystem | binary                      |
    >     | character_set_results    | latin1                      |
    >     | character_set_server     | latin1                      |
    >     | character_set_system     | utf8                        |
    >     | character_sets_dir       | /usr/share/mysql/charsets/ |
    >     +--------------------------+----------------------------+
    >     8 rows in set (0.01 sec)
    >     ```
    >
    > -   设置mysql默认字符集为utf8:
    >
    >     修改MYSQL 默认字符集：
    >     
    >     ```
    >     cd /etc
    >     vim my.cnf
    >     ```
    >     
    >     添加到my.cnf
    >     
    >     ```
    >     [mysqld]
    >     
    >     character-set-server=utf8
    >     
    >     [client]
    >     
    >     default-character-set=utf8
    >     
    >     [mysql]
    >     
    >     default-character-set=utf8
    >     ```
    >     
    >     ```
    >     mysql> show variables like '%char%';
    >     +--------------------------+----------------------------+
    >     | Variable_name            | Value                       |
    >     +--------------------------+----------------------------+
    >     | character_set_client     | utf8                        |
    >     | character_set_connection | utf8                        |
    >     | character_set_database   | latin1                      |
    >     | character_set_filesystem | binary                      |
    >     | character_set_results    | utf8                        |
    >     | character_set_server     | latin1                      |
    >     | character_set_system     | utf8                        |
    >     | character_sets_dir       | /usr/share/mysql/charsets/ |
    >     +--------------------------+----------------------------+
    >     8 rows in set (0.00 sec)
    >     ```

1.  环境变量

    > -   [mysql 在Docker上配置官方文档](https://dev.mysql.com/doc/refman/5.7/en/docker-mysql-more-topics.html)
    > 
    >     eg :
    > 
    >     ```
    >     sudo docker run -p 3305:3306 -d -e MYSQL_USER="or" -e MYSQL_PASSWORD="pwd123456" -e MYSQL_ROOT_PASSWORD="rootpwd" --name mysql02 mysql/mysql-server --character-set-server=utf8 --collation-server=utf8_general_ci
    >     ```
    > 
    > -   挂载到宿主机（Host Volume）
    > 
    >     +   建立host上的文件夹：
    >                   
    >         ```
    >         mkdir -p docker/mysql
    > 
    >         cd docker/mysql
    > 
    >         mkdir config data
    >         ```
    > 
    >     +   在config 文件夹下添加文件一个my.cnf
    > 
    >         ```
    >         vi my.cnf
    >         ```
    > 
    >     +   添加以下配置，后期改动重启mysql镜像即可
    >     
    >         ```
    >         [mysqld]
    >         user=mysql
    >         character-set-server=utf8 
    >         [client]
    >         default-character-set=utf8 
    >         [mysql]
    >         default-character-set=utf8
    >         ```
    > 
    >     +   使用volumn挂载外部文件
    > 
    >         ```
    >         sudo docker run -d -p 3304:3306 -e MYSQL_ROOT_PASSWORD=pwd123456 -e MYSQL_ROOT_HOST=% --name mysql03 -v=/docker/mysql/config/my.cnf:/etc/my.cnf -v=/docker/mysql/data:/var/lib/mysql    mysql/mysql-server
    >         ```
    > 
    >         **删除镜像，同时注意删除data文件夹，否则会有密码缓存**


## TODO

1. mysql 日志  
    my.cnf 里面添加配置即可
    ```
    [mysqld]
    user=mysql
    character-set-server=utf8
    # log_bin=D:\mysql-5.7.15-winx64\log\mysql-bin.log
    # log_error=D:\mysql-5.7.15-winx64\log\mysql.err
    # long_query_time=1
    # slow_query_log=ON
    # slow_query_log_file=D:\mysql-5.7.15-winx64\log\mysql-slow.log
    general_log=ON
    general_log_file=/var/lib/mysql/mysql03.log
    slow_query_log = on
    slow-query-log-file=/var/lib/mysql/mysql03-slow.log

    ```