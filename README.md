# Q&A for PA2018 NUAA
PA2018常见问题解答（持续更新，更新频率1-2天/次），欢迎star
---
**提问以前务必翻看！！**
# PA0

1.近期90%以上的问题**来自手误**，麻烦出问题的同学自己仔细**逐个字符**检查一下敲的内容（`dockerfile`，修改`debian的update源`，敲`命令`等等）有没有打错的（前几天一个比较极端的例子是`debian`打成`debain`搞了半个小时，昨天还有一个把参数的-打成全角横杠，搞了一个小时）。
* 附常见手误：
```
debian和debain
docker build -t和docker built -d
半角字符和全角字符（多半是输入法造成的）
（持续更新中...）
```

2.关于vmware的host-only，给一个参考博文，[请点击这里](http://blog.csdn.net/u013121305/article/details/49505679)
搜索关键词：【`linux host-only vmware`】，另外经验证使用虚拟机网卡的`桥接模式`可以连通虚拟机的22端口，可以尝试。

3.docker命令无法识别，不是网络问题，是docker安装的有问题，解决方法是`卸载重装`，并检查`hyper-v`等是否处于正常打开状态

4.拉取镜像慢和apt-get慢，以及拉取镜像1/11失败，apt-get失败等，解决办法都是挂VPN和换国内源（搜索关键字：【`docker 更换国内源`】【`debian 更换国内源`】【`apt-get 阿里源`】）
* docker的镜像加速（摘自菜鸟教程）：
鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，我们可以需要配置加速器来解决，我使用的是网易的镜像地址：http://hub-mirror.c.163.com。
新版的 Docker 使用 /etc/docker/daemon.json（Linux） 或者 %programdata%\docker\config\daemon.json（Windows） 来配置 Daemon。
请在该配置文件中加入（没有该文件的话，请先建一个）：
```json
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```

5.再再然后有时候遇到vim修改某某系统文件失败，麻烦用`sudo vim`来修改

6.遇到docker好像开启了容器但是连不上，多半是上次用完没停止容器，然后挂起假死了，`stop一下，然后重启下电脑`，基本上都正常了

7.开始做PA0以后**不要去瞎搞电脑**（包括重装系统，升级系统，装某某驱动，重装docker或者虚拟机什么的），这个搞坏了**只有从头做**，除非自己网上找代码仓库（比如`码云`，`github`，`coding`这些）存代码，这种情况也要重做PA0，所以这一个学期，电脑坏了只要还能将就用能做PA，就不要修电脑，期末结束了再修，否则影响了进度问题很严重

8.重修同学，如果不想重新搭建docker环境，可以将就沿用去年的`虚拟机环境`（前提当然是你没删），但是请把去年的ics目录删掉（不删也罢，但是别搞错了哪个是哪个），2018和2017的PA用的代码框架不同（这个对比下讲义就显而易见了），因此去年不管做到哪挂的。。代码几乎没用了。。

#	PA 2.1

重装了系统，今天重新弄pa的时候刚好碰到了大家的问题，就是在`cputest`下运行

```bash
make ARCH=x86-nemu ALL=dummy run
```

的时候报错

```bash
Makefile.checkout:No such file or directory
make : *** No rule to make target '/home/xxx/ics2017/nexus-am/Makefile.check'. Stop
```

这个其实主要去看看`Makefile`就大概知道什么错了，主要就是`AM_HOME`环境变量没找到

有很多同学init到现在都不重启，导致bash没把环境变量引进。或者用zsh，init的时候默认是写到`.bashrc`，`.zshrc`就不会有相关的环境变量，而zsh每次启动都是加载的是`.zshrc`，这就导致了变量缺失的问题

所以如果是zsh，就在用户目录下修改`.zshrc`，在文件末尾添加如下内容：(没有'#'号,xxx为你的用户名)

```Bash
export NEMUHOME=/home/xxx/ics2017/nemu
export AM_HOME=/home/xxx/ics2017/nexus-am
export NAVY_HOME=/home/xxx/ics2017/navy-apps
```

然后重启容器

```Bash
>	docker restart xxx(你的容器名称)
```

如果用的是bash的话，确保`.bashrc`有那三条内容直接重启就好了