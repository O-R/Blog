# 常用命令

## apt

## 用户/用户组

1. 查看所有用户 `cat /etc/passwd`
1. 查看所有用户组 `cat /etc/group`
1. 添加用户 `sudo adduser or`
1.   **将用户添加到 sudoers**   
     > root用户下使用 `visudo`   
     > sudo vim /etc/sudoers  
     
     **修改文件如下：**  
     > \# User privilege specification  
     > root ALL=(ALL) ALL  
     > or ALL=(ALL) ALL  

## 查看所有服务端口 `cat /etc/services | less`

## 防火墙 `ufw`

## 文件

### 权限 `chmod`

### 列举文件 `ls`

### 移动/复制/剪切 `mv`

### 删除 `rm`

### 解压缩 `rar`
