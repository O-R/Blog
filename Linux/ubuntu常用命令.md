# 常用命令

## apt

## 用户/用户组

1. 查看所有用户 `cat /etc/passwd`
1. 查看所有用户组 `cat /etc/group`
1.添加用户 `sudo adduser or`
1.   **将用户添加到 sudoers**  
     > root用户下使用 `visudo`   
     > sudo vim /etc/sudoers 
     
     **修改文件如下：**  
     > \# User privilege specification  
     > root ALL=(ALL) ALL  
     > or ALL=(ALL) ALL  

## 进程、端口占用

1. `netstat -tln`：查看端口占用
1. `lsof -i:port`：查看端口进程信息
1. 查看所有服务端口 `cat /etc/services | less`

## 防火墙 `ufw`

## 文件

### 权限 `chmod`

### 列举文件 `ls`

### 移动/复制/剪切 `mv`

### 删除 `rm`

### 解压缩 `rar`

### 代理测试

```
# 访问各大网站，若均有网页源码输出则配置成功
curl -4skL https://www.baidu.com
curl -4skL https://www.google.com
curl -4skL https://www.google.com.hk
curl -4skL https://www.google.co.jp
curl -4skL https://www.youtube.com
curl -4skL https://www.facebook.com
curl -4skL https://www.wikipedia.org

# 获取当前 IP 地址，应该显示本机 IP
curl -4skL http://ip.chinaz.com/getip.aspx
```
