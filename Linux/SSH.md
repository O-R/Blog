# SSH 

## 1. SSH原理

- 中间人攻击

SSH之所以能够保证安全，原因在于它采用了公钥加密。
整个过程是这样的：
1. 远程主机收到用户的登录请求，把自己的公钥发给用户。
1. 用户使用这个公钥，将登录密码加密后，发送回来。
1. 远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。https://github.com/O-R/Blog.git

这个过程本身是安全的，但是实施的时候存在一个风险：如果有人截获了登录请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪。因为不像https协议，SSH协议的公钥是没有证书中心（CA）公证的，也就是说，都是自己签发的。

可以设想，如果攻击者插在用户与远程主机之间（比如在公共的wifi区域），用伪造的公钥，获取用户的登录密码。再用这个密码登录远程主机，那么SSH的安全机制就荡然无存了。这种风险就是著名的"中间人攻击"（Man-in-the-middle attack）。

- 公钥登录

公钥登录的步骤：
1. 客户端发起请求，请求内容包括公钥等
1. 远程主机接到请求，寻找到对应公钥，并用公钥将**质询**（一段随机字符串吧）加密后发送给客户端
1. 客户端使用私钥+私钥对应的口令（passphrase，如果有）对**质询**进行解密，并将解密后的**质询**以及当前会话ID进行MD5加密后，发送给远程主机
1. 远程主机使用同样方法加密**质询**及当前会话ID，并进行比对，匹配上了则登录成功。

此为远程主机公钥加密，客户端私钥解密的说法，也有私加公解的说法，感觉差不多。。

## 2. SSH登录（RSA）

- 生成密钥对


```
ssh-keygen -t rsa -b 2048
```

语法：

```
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```
命令解释：
`ssh-keygen` = 用于创建密钥的程序
`-t rsa` = 要创建的 RSA 格式密钥类型 [wikipedia]parens 结尾 -b 2048 = 密钥的位数
`-C "azureuser@myserver"` = 追加到公钥文件末尾以便于识别的注释。 通常以电子邮件作为注释，但也可以使用任何最适合基础结构的事物。

```
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

- 使用 SSH 密钥对

公钥默认存储在 ~/.ssh/id_rsa.pub 中，除非你在创建该公钥时更改了位置。如果不熟悉 SSH 公钥，则可通过运行 cat 来查看公钥（如下所示），注意需将 ~/.ssh/id_rsa.pub 替换为你自己的公钥文件位置：

``` bash
cat ~/.ssh/id_rsa.pub
```

通过 SSH 使用 VM 的 IP 地址或 DNS 名称连接到 VM（记住将下面的 azureuser 和 myvm.westus.cloudapp.azure.com 替换为管理员用户名和完全限定域名或 IP 地址）：


``` bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果你是第一次登录远程主机，系统会出现下面的提示：

```
$ ssh user@host
The authenticity of host 'host (12.18.429.21)' can't be established.
RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.
Are you sure you want to continue connecting (yes/no)?
```

这段话的意思是，无法确认host主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？

所谓"公钥指纹"，是指公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。上例中是`98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d`，再进行比较，就容易多了。

很自然的一个问题就是，用户怎么知道远程主机的公钥指纹应该是多少？回答是没有好办法，远程主机必须在自己的网站上贴出公钥指纹，以便用户自行核对。

假定经过风险衡量以后，用户决定接受这个远程主机的公钥。

```
　　Are you sure you want to continue connecting (yes/no)? yes
```

系统会出现一句提示，表示host主机已经得到认可。

```
　　Warning: Permanently added 'host,12.18.429.21' (RSA) to the list of known hosts.
```

然后，会要求输入密码。

```
　　Password: (enter password)
```

如果密码正确，就可以登录了。
当远程主机的公钥被接受以后，它就会被保存在文件$HOME/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。
每个SSH用户都有自己的known_hosts文件，此外系统也有一个这样的文件，通常是/etc/ssh/ssh_known_hosts，保存一些对所有用户都可信赖的远程主机的公钥。


## 3. 参考链接

- [如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](https://docs.microsoft.com/zh-cn/azure/virtual-machines/linux/mac-create-ssh-keys)
- [详细演练了如何为 Azure 中的 Linux VM 创建 SSH 密钥对和其他证书](https://docs.microsoft.com/zh-cn/azure/virtual-machines/linux/create-ssh-keys-detailed)
- [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

