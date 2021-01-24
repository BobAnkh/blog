Title: kswapd0挖矿病毒的发现与清除
Date: 2020-07-10 17:30
Category: Linux
Tags: Linux, Server, Ubuntu, Virus
Slug: blog/kswapd0_virus
Author: BobAnkh
Summary: 对于近期由于安装docker时趁虚而入的伪装成kswapd0进程的挖矿病毒的发现与清除
Illustration: background.jpg

[TOC]

> 写下这篇博客的原因是实验室的服务器在安装docker之后不幸感染上了挖矿病毒，便将发现与清除的方法记录于此，如有错漏，恳请指正

## 起因与状况

因为实验需要，近期在实验室的服务器上使用tke搭建k8s平台，在此过程中，k8s官方要求将防火墙关闭。

而完成搭建之后，发现有一个kswapd0程序占用了比较高的cpu使用率。

一开始以为或许是安装进程的原因，但后续卸载了installer并清除了tke残余内容，发现该进程仍然存在，于是推测该进程应当存在问题。

该进程可以直接kill掉，但是reboot之后又会重新出现，状况如下：

![htop](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/kswapd0/a1181b1edc2ab.png)

## 探查根源

根据上面的进程信息，可以看到该进程执行的用户是vagrant，这就让人感到奇怪了，于是用命令`netstat -antlp`查看了应用连接：

![netstat](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/kswapd0/d07094470a348.png)

发现存在两个连接是通向2个奇怪的ip地址的，其中之一就是kswapd0，还有一个是rsync（是病毒缓存文件）：

检查这两个ip地址发现都是荷兰的ip，而且是同一个段的，查询发现也是知名的挖矿ip：

![45.9.148.125](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/kswapd0/7b54ce07f625f.png) ![45.9.148.99](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/kswapd0/b69342dde711a.png)

于是便查看这个程序的根源出自哪里，使用命令`ls -l /proc/2971/exe`探查：

发现它指向`/home/vagrant/`下的一个`.configrc/a/kswapd0`，进入`.configrc/`文件夹探查发现，整个文件夹应该都是有问题的

到临时目录`/tmp`中，使用命令`ls -sa | grep vagrant`探查，也可以看到与vagrant用户相关的奇怪的文件

此时，还需要检查出现问题用户的crontab，看是否被写入了定时计划，否则可能定时或者reboot之后又会启动病毒，使用命令`crontab -l -u vagrant`检查：

![crontab](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/kswapd0/41d832cfb6a41.png)

另外，对于该用户，还需要检查其`.ssh`目录下`authorized_keys`是否被配置了后门的ssh的key，本次发现该公钥如下，也是一个知名公钥了：

```txt
AAAAB3NzaC1yc2EAAAABJQAAAQEArDp4cun2lhr4KUhBGE7VvAcwdli2a8dbnrTOrbMz1+5O73fcBOx8NVbUT0bUanUV9tJ2/9p7+vD0EpZ3Tz/+0kX34uAx1RV/75GVOmNx+9EuWOnvNoaJe0QXxziIg9eLBHpgLMuakb5+BgTFB+rKJAw9u9FSTDengvS8hX1kNFS4Mjux0hJOK8rvcEmPecjdySYMb66nylAKGwCEE6WEQHmd1mUPgHwGQ0hWCwsQk13yCGPK5w6hYp5zYkFnvlC8hGmd4Ww+u97k6pfTGTUbJk14ujvcD9iUKQTTWYYjIIu5PmUux5bsZ0R4WFwdIe6+i6rBLAsPKgAySVKPRK+oRw==
```

## 病毒清除

首先，先清理出现问题用户的crontab，使用命令`crontab -e -u vagrant`后，将内容全部清除

接着，用`kill -9 2971`命令将**kswapd0**进程杀掉，同时，用命令`kill -9 2339`将**rsync**进程也杀掉

随后删除`/home/vagrant/`目录下`.configrc/`和`/tmp/`目录下`.X25-unix/dota3.tar.gz`和`.X25-unix/.rsync/*`

至此，清除工作就完成了，后续可以观察病毒是否会再生

另外，建议除特殊要求外，时刻将防火墙开启，并且，如有需要，可以自己编写脚本检查各用户是否存在奇怪的crontab定时计划

这里给出一个可行脚本：

```bash
cat /etc/passwd | awk -F":" '{print $1}' | while read -r line
do
        echo $line
        crontab -l -u $line
done
```
