# 下载安装 `nvm-windows`

1. 在要安装的文件夹下，右键-在此处打开命令行，在命令行中执行：git clone https://github.com/coreybutler/nvm-windows.git
1. 直接在Release页面下载，下载手动安装版本`nvm-noinstall.zip`：https://github.com/coreybutler/nvm-windows/releases

# 配置

## `nvmw`配置

1. 更改环境变量，有两个是新增：`NVM_HOME`,`NVM_SYMLINK`，一个是修改：`Path`
1. `NVM_HOME`：解压安装包的路径，如果是自动安装版本，默认是：`C:\Users\<username>\AppData\Roaming\nvm`
1. `NVM_SYMLINK`：这个路径必须不存在，nvmw会自动生成，此路径用于辨认当前使用的是哪个版本的Nodejs
1. `Path`：在这个系统环境变量后边加上`%NVM_HOME%;%NVM_SYMLINK%`,注意原`Path`变量要`;`结尾，没有便加上
1. 在安装目录下添加`settings.txt`，内容如下：
    * root: `NVM_HOME`的内容
    * path: `NVM_SYMLINK`的内容
    * proxy: 默认设置为 "none"。如果需要配置，可以通过命令行
    * arch: 32/64，操作系统是32位还是64位
  
```
root: C:\Users\Corey\AppData\Roaming\nvm
path: C:\Program Files\nodejs
arch: 64
proxy: none
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```
