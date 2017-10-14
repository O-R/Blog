# 在VM中设置Ubuntu静态IP

## `NAT`模式

1. VM中打开 __编辑 -> 虚拟机网络编辑器__
1. 获取到__可用IP范围、网关和子网掩码__
1. 通过命令配置固定IP

如下图：


可以获取到__可用IP范围、网关和子网掩码__

- `可用IP范围`：192.168.175.128~192.168.175.254
- `网关`：255.255.255.0
- `子网掩码`：192.168.175.2

修改配置：

```
auto lo
iface lo inet loopback

# Assgin static IP by eric on 26-SEP-2012
auto enss33
iface enss33 inet static
address 192.168.175.139 #change to your static IP
netmask 255.255.255.0  #change to your netmask
gateway 192.168.175.2    #change to your getway
#We must specify dns-nameserver here
#in order to get internet access from host
dns-nameservers 192.168.175.2
```

重启ubuntu网卡：`sudo /etc/init.d/networking restart`

重启系统：`reboot`
