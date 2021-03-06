# 系统常规配置

## 文件传输

安装 lrzsz

```
apt install lrzsz
```

bash命令行输入 `rz -e` ：打开当前终端文件进行传输

`sz -e 文件在远程机器上路径`:打开当前终端选择下载文件位置

## 语言环境

### bash终端

- 查看当前系统的语言环境 `locale`

- 查看当前系统已安装的语言 `locale -a`

- 切换（并自动安装）对应选择的默认语言 `sudo dpkg-reconfigure locales`

- 安装缺少的语言 `sudo locale-gen zh_CN.UTF-8`

- 编辑配置文件

```
ubuntu@VM-14-193-ubuntu:~$ vim ~/.profile
# 添加下面内容
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

### 远程桌面

安装文泉驿字体 `apt install wqy* -y`

## xfce4 解压缩工具

安装命令:

```
apt install thunar-archive-plugin -y
```

## 文本编辑器

安装命令:

```
apt install gedit -y
```

或

```
apt install mousepad -y
```

## xfce4 软件包列表

其中依赖必需的包在你执行apt-get iinstall xfce4的时候就已经完成安装了

除了Lib库文件、主要有以下的包

```
gtk2-engines-xfce          GTK+-2.0 对于Xfce的主题显示支持 
orage                                   Xfce 桌面环境的日历和时间管理
thunar                                  Xfce 的文件管理器 
xfce4-appfinder              Xfce中运行命令和应用程序查找、就是应用程序菜单第一个项目
xfce4-mixer                      Xfce中的混音器、简单说就是音量外放和话题之类的控制
xfce4-panel                       Xfce中的面板管理器
xfce4-session                   Xfce中的会话管理器 
xfce4-settings                  Xfce中的设置（是指图形界面的设置、就是打开菜单能看到的那个设置）
xfce4-utils                        Xfce中各个方面的工具
xfconf                                  Xfce设置管理器中的实用工具
xfdesktop4                        xfce的桌面背景、图标、还有菜单管理都由这个提供
xfwm4                                Xfce中的视窗管理器
desktop-base                  Debian 桌面环境的公共基础

tango-icon-theme        Tango 主题图标 
thunar-volman               Thunar 文件管理器的音量控制扩展 
xfce4-notifyd                   可视化的通知守护进程
```

以上这些软件包是你在安装Xfce4的时候因为依赖安装的包、其中最后的三个在Debian软件仓库里面是提示推荐的包。


下面说下Xfce中可以用来扩充功能的包、就是这些包在最小化配置的时候不会默认安装好、但是没有它们有些功能不能使用

xfprint4                                                                      Xfce4 桌面环境下使用的打印机管理器。

Xfce4-goodies是由下面的包填充的、也就是说、你安装 xfce4-goodies其实是安装的下面这些软件包

```
xfce4-artwork                          Xfce桌面环境美化的扩展包、会安装一些图标、主题、壁纸
xfce4-battery-plugin                电池电量的显示器
xfce4-clipman-plugin                剪切板历史记录查看
xfce4-cpufreq-plugin                设置CPU频率
xfce4-cpugraph-plugin               CPU利用率查看
xfce4-datetime-plugin               日期和时间插件
xfce4-diskperf-plugin               磁盘情况监控
xfce4-fsguard-plugin                文件系统监控
xfce4-genmon-plugin                  显示一些命令提示
xfce4-mailwatch-plugin              邮件通知
xfce4-mount-plugin                   挂载管理
xfce4-netload-plugin                网络
xfce4-notes-plugin                   笔记
xfce4-places-plugin                  快速管理收藏夹、最近使用、可移除驱动器
xfce4-quicklaunchers                快速启动
xfce4-sensors-plugin                传感器、可以查看硬件温度
xfce4-smartbookmark-plugin        收藏夹
xfce4-systemload-plugin            系统加载项查看
xfce4-timer-plugin                   计时器
xfce4-verve-plugin                   提供命令记忆功能
xfce4-wavelan-plugin                无线网运行情况
xfce4-weather-plugin                天气预报
xfce4-xkb-plugin                      键盘配置
thunar-archive-plugin               压缩文件的解压插件
thunar-media-tags-plugin           提供媒体文件标签功能
mousepad                                一个简单的文本编辑器
ristretto                               图像查看器
xfburn                                   DVD刻录软件
xfce4-dict                              词典
xfce4-notifyd                          通知
xfce4-screenshooter                  截图
xfce4-taskmanager                    任务管理器
xfce4-terminal                        终端
xfce4-cellmodem-plugin            调制解调器
xfce4-linelight-plugin            一个搜索程序
xfce4-messenger-plugin            DBus消息通知
xfce4-minicmd-plugin               额外的终端软件
xfce4-mpc-plugin, xfmpc           Music Player Daemon播放器守护程序，管理播放列表和音乐数据库
xfce4-radio-plugin                  收音机插件
xfce4-xfapplet-plugin              提供GNOME环境中的一些小应用程序支持
xfswitch-plugin                      用户快速切换
xfce4-hdaps                           ThinkPads的HDAPS插件
thunar-thumbnailers                为文件管理器提供图像预览功能
gigolo                                  GIO/GVfs虚拟文件系统的远程管理前端
parole                                  视频播放器
xfce4-power-manager                电源管理
```

以上软件包可以单独安装的、所以看你需要添加即可

