
# Ubuntu 14.04下安装和配置ShadowSocks服务器

## 安装ShadowSocks服务器

在Ubuntu下安装ShadowSocks服务器端其实很简单，只需要键入下面几条命令就可以了：

```
sudo apt-get update  
sudo apt-get install python-gevent python-pip  
sudo pip install shadowsocks  
apt-get install python-m2crypto  
```

## 配置ShadowSocks服务器

ShadowSocks服务器的配置文件可以放到任何你想放的位置，起任何的名字。
出于习惯，可以将其放在/etc目录下，名字为shadowsocks.json：

```
vim /etc/shadowsocks.json  
```

在编辑器里面修改文件内容为：

```
{  
    "server":"serverip",  
    "server_port":8388,  
    "local_port":1080,  
    "password":"Your Password",  
    "timeout":600,  
    "method":"aes-256-cfb"  
}  
```

你可以更改“server_port”字段，修改ShadowSocks服务器的端口。
还有“method”字段指定的是加密算法，ShadowSocks服务器支持多种加密算法（aes-256-cfb，aes-192-cfb，aes-128-cfb，rc4-md5，des-cfb等等），可以根据需要自己配置。

## 运行ShadowSocks服务器

注意ShadowSocks服务器要在root用户下运行，所以一开始请切换用户。
然后只需要输入下面的命令就可以了：

```
ssserver -c /etc/shadowsocks.json -d start 
```

-c参数用来指定ShadowSocks服务器端配置文件的路径，只要设置成前一步修改的配置文件的路径名就好了。

## 停止ShadowSocks服务器

同样要在root用户下，执行下面的命令即可：

```
ssserver -c /etc/shadowsocks.json -d stop  
```
