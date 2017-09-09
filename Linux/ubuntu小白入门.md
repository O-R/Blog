# 登录

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



