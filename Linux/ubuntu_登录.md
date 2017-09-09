# 一、 登录

## 1. 生成SSH密钥

### 生成命令：

```
ssh-keygen -t rsa -b 2048
```

### 查看密钥(位于`C:\Users\用户名\AppData\Local\lxss\home\or\.ssh`)

```
cat ~/.ssh/id_rsa.pub
```

### 使用 ssh-agent 来存储私钥密码(这种方法好像并没什么卵用)

为了避免在每次 SSH 登录时键入私钥文件密码，可以使用 ssh-agent 来缓存私钥文件密码。 如果使用 Mac，OSX Keychain 在用户调用 ssh-agent时会安全存储私钥密码。

验证并使用 ssh-agent 和 ssh-add 将密钥文件的情况通知给 SSH 系统，使密码不需以交互方式使用。

```
eval "$(ssh-agent -s)"
```

现在，使用命令 ssh-add 将私钥添加到 ssh-agent。

```
ssh-add ~/.ssh/id_rsa
```

私钥密码现在存储在 ssh-agent 中。

### 使用 ssh-copy-id 将密钥复制到现有 VM

如果已创建 VM，则可使用以下命令将新的 SSH 公钥安装到 Linux VM：

```
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

### 创建并配置 SSH 配置文件

建议的最佳做法是，创建并配置 ~/.ssh/config 文件，以便加速登录和优化 SSH 客户端行为。
以下示例显示了标准配置。

创建文件

```
touch ~/.ssh/config
```

编辑文件以添加新的 SSH 配置：

```
vim ~/.ssh/config
```

示例 ~/.ssh/config 文件：

```
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

此 SSH 配置可以指定每个服务器的部分，以便每个服务器使用自己的专用密钥对。 默认设置 (Host *) 适用于不匹配配置文件中更高级别特定主机的任何主机。
配置文件解释

`Host` = 在终端上调用的主机的名称。 `ssh fedora22` 告知 SSH 使用标记为 Host fedora22 的设置块中的值。注意：主机可以是符合用途的任何标签，并不代表任何服务器的实际主机名。

`Hostname 102.160.203.241` = 所访问的服务器的 IP 地址或 DNS 名称。

`User ahmet` = 登录到服务器时要使用的远程用户帐户。

`PubKeyAuthentication yes` = 告知 SSH 要使用 SSH 密钥来登录。

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 要用于身份验证的 SSH 私钥和对应的公钥。

### 在不提供密码的情况下使用 SSH 连接到 Linux

获得 SSH 密钥对并配置 SSH 配置文件后，便可以快速安全地登录到 Linux VM 了。 首次使用 SSH 密钥登录到服务器时，命令会提示用户输入该密钥文件的通行短语。

```
ssh fedora22
```

命令解释

执行 ssh fedora22 后，SSH 先从 Host fedora22 块中找到并加载所有设置，并从最后一个块 (Host *) 中加载所有剩余设置。

## 2、远程桌面登录

### 使用 `apt` 安装轻型 `xfce4` 桌面环境

```
sudo apt-get update
sudo apt-get install xfce4
```

### 安装和配置远程桌面服务器

安装桌面环境后，请配置远程桌面服务来侦听传入连接。 xrdp 是大多数 Linux 分发版中提供的开源远程桌面协议 (RDP) 服务器，可与 xfce 完美配合。 在 Ubuntu VM 上安装 xrdp，如下所示：

```
sudo apt-get install xrdp
```

告诉 xrdp 在启动会话时要使用的桌面环境。 配置 xrdp 以使用 xfce 作为桌面环境，如下所示：

```
echo xfce4-session >~/.xsession
```

重新启动 xrdp 服务使更改生效，如下所示：

```
sudo service xrdp restart
```

设置本地用户帐户密码

如果在创建 VM 时已为用户帐户创建密码，请跳过此步骤。 如果仅使用 SSH 密钥身份验证，并且未设置本地帐户密码，请在使用 xrdp 之前指定密码以登录到 VM。 xrdp 无法接受使用 SSH 密钥进行身份验证。 以下示例为用户帐户 azureuser 指定密码：

```
sudo passwd azureuser
```

**备注 :指定密码不会将 SSHD 配置更新为允许密码登录（如果当前不允许）。**

### 为远程桌面流量创建网络安全组规则

若要允许远程桌面流量到达 Linux VM，需要创建网络安全组规则以允许端口 3389 上的 TCP 访问 VM。
至此，就可以用windows远程桌面登录了


