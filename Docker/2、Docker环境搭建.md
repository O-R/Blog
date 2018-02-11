# 在**Ubuntu16.04**系统下安装Docker

## 使用镜像仓库进行安装

### 设置镜像仓库

1.  更新 apt 软件包索引：
 
    > ```
    >  $ sudo apt-get update
    > ```

1.  安装软件包，以允许 apt 通过 HTTPS 使用镜像仓库：

    >```
    > $ sudo apt-get install \
    >     apt-transport-https \
    >     ca-certificates \
    >     curl \
    >     software-properties-common
    >```

1.  添加 Docker 的官方 GPG 密钥：

    >```
    > $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    >```

1.  验证密钥指纹是否为 `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`。

    >```
    > $ sudo apt-key fingerprint 0EBFCD88
    >
    > pub   4096R/0EBFCD88 2017-02-22
    >       Key fingerprint = 9DC8 5822 9FC7 DD38  854A  E2D8 8D81 803C 0EBF CD88
    > uid                  Docker Release (CE deb) <docker@docker.com>
    > sub   4096R/F273FCD8 2017-02-22
    >```

1.  使用下列命令设置 `stable` 镜像仓库。您始终需要使用 `stable` 镜像仓库，即使您还需要通过 `edge` 或 `testing` 镜像仓库安装构建也是如此。如需添加 `edge` 或 `testing` 镜像仓库，请在以下命令中的词 `stable` 之后添加词 `edge` 和/或 `testing`。

    > 注：下面的 `lsb_release -cs` 子命令用于返回您的 Ubuntu 分发版，例如 xenial。

    > 有时，在 Linux Mint 等分发版中，您可能必须将 $(lsb_release -cs) 更改为您的父代 Ubuntu 分发版。例 如：如果您使用的是 Linux Mint Rafaela，您可以使用 trusty。

    > amd64：
    >
    >```
    > $ sudo add-apt-repository \
    >    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    >    $(lsb_release -cs) \
    >    stable"
    >```

    > 注：从 Docker 17.06 开始，还会将 stable 版本推送到 edge 和 testing 镜像仓库。
了解 stable 和 edge 渠道。

### 安装 DOCKER CE

1.  更新 apt 软件包索引。

    > ```
    >  $ sudo apt-get update
    > ```
    
1.  安装最新版本的 Docker CE，或者转至下一步以安装特定版本。将替换任何现有的 Docker 安装版本。
    >
    >```
    > $ sudo apt-get install docker-ce
    >```

    > 警告： 如果您启用了多个 Docker 镜像仓库，进行安装 或者更新而不在 apt-get install 或 apt-get update 命令中指定版本将始终安装可用的最高版本， 这可能无法满足您的稳定性需求。

1.  在生产系统中，您应该安装特定版本的 Docker CE，而不是始终使用最新版本。此输出将被截断。列出可用版本。

    > ```
    > $ apt-cache madison docker-ce
    > docker-ce | 17.06.0~ce-0~ubuntu-xenial |  https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    > ```

    > 此列表的内容取决于启用了哪些镜像仓库，并且将特定于 您的 Ubuntu 版本（在本示例中，由版本中的 xenial 后缀   表示）。选择一个特定版本进行安装。第二列为版本字符  串。第三列为镜像仓库名称，它表示软件包来自哪个镜像仓    库并按扩展其稳定性级别列出。如需安装特定版本，请将版  本字符串附加到软件包名称，并使用等号 (=) 分隔它们：

    > ```
    > $ sudo apt-get install docker-ce=<VERSION>
    > ```

    Docker 守护进程将自动启动。

1.  验证是否正确安装了 Docker CE，方法是运行 hello-world 镜像。

    > ```
    > $ sudo docker run hello-world
    > ```

    > 此命令将下载一个测试镜像并在容器中运行它。容器运行 时，它将输出一条参考消息并退出。

    > Docker CE 已安装并且正在运行。您需要使用 sudo 运行 Docker 命令。继续执行 Linux 安装后步骤以允许非特权用户运行 Docker 命令，以及了解其他可选配置步骤。

## 参考链接

- [官方中文安装](https://docs.docker-cn.com/engine/installation/linux/docker-ce/ubuntu)
- [官方英文安装](https://docs.docker.com/install/linux/docker-ce/ubuntu/)