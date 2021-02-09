Title: 在Ubuntu下挂载swap分区
Date: 2021-2-8 12:00
Category: Linux
Tags: Linux, Ubuntu, Server
Slug: blog/mount_swap
Author: BobAnkh
Summary: 在Ubuntu下挂载swap分区(交换分区)与相关设置
Illustration: background.jpg

[TOC]

> 在服务器内存不够的情况下，我们通常需要添加一些**swap**分区用于作为内存的备用，当然它比真实的内存要慢很多，所以需要谨慎考虑是否启用和选择其大小

## 检查系统的交换分区情况、内存情况、硬盘空间情况

使用`sudo swapon --show`命令来检查系统是否有交换分区，如果没有任何结果或者没有任何显示，说明系统当前没有可用的交换空间

若有交换分区，输出类似以下结果：

```console
NAME      TYPE SIZE USED PRIO
/swapfile file   8G   0B   -2
```

也可以使用`free -h`来一并检查内存情况和交换分区情况：

```console
              total        used        free      shared  buff/cache   available
Mem:           62Gi       477Mi        57Gi       2.0Mi       4.6Gi        61Gi
Swap:         8.0Gi          0B       8.0Gi
```

可以使用`df -h`命令来检查硬盘使用情况：

```console
Filesystem      Size  Used Avail Use% Mounted on
udev             32G     0   32G   0% /dev
tmpfs           6.3G  2.3M  6.3G   1% /run
/dev/sda2       439G   52G  366G  13% /
tmpfs            32G     0   32G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs            32G     0   32G   0% /sys/fs/cgroup
tmpfs           6.3G     0  6.3G   0% /run/user/1001
```

## 创建swap

我们创建一个8G大小的交换文件：

```shell
sudo fallocate -l 8G /swapfile
```

接下来，我们将其变换成交换内存，不过为了保证安全，需要调整其权限，以便只有拥有**root**权限的用户才能读取文件内容：

```shell
sudo chmod 600 /swapfile
```

接下来通过以下命令将其标记为交换空间：

```shell
sudo mkswap /swapfile
```

然后启用该交换空间

```shell
sudo swapon /swapfile
```

最后，我们还需要永久保留**swap**设置，否则一重启就没了。我们可以通过将**swap**文件添加到`/etc/fstab`文件中来改变这一点：

首先备份`/etc/fstab`文件以防出错：

```shell
sudo cp /etc/fstab /etc/fstab.bak
```

接下来将**swap**文件信息添加到`/etc/fstab`文件的末尾：

```shell
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

我将以上过程汇总成了一个bash脚本[mount_swap.sh](https://github.com/BobAnkh/blog/blob/master/assets/mount_swap.sh)，可以使用如下方式下载运行，第一个参数为想要的swap分区大小：

```shell
chmod +x mount_swap.sh
./mount_swap.sh 8G
```

## swappiness属性

> 挂载**swap**分区后，我们也可以额外调整**swappiness**属性的值，以根据自己情况实现更好的性能

该属性配置系统将数据从**RAM**交换到**swap**的频率, 值介于0和100之间，表示百分比。

要记住一点，与**swap**交互花费的时间比与**RAM**的交互更长，并且会导致性能的显著下降，也即系统更少依赖**swap**分区通常会使你的系统更快，但是它又可补充物理内存的缺乏。所以根据自身实际情况合理设置该属性有助于取得更好的整体性能。

如果**swappiness**值接近0，除非绝对必要，内核将不会将数据交换到硬盘(即swap)；**swappiness**值接近100时，内核将尝试将更多的数据放入交换中，以保持更多的RAM空间。

运用`cat /proc/sys/vm/swappiness`命令查看当前的**swappiness**值，默认值为60

我们可以使用`sysctl`命令将**swappiness**设置为不同的值，如要将**swappiness**设置为20：

```shell
sudo sysctl vm.swappiness=20
```

该设置将保持到系统下次重新启动，如果想要在重启之后也生效，我们可以通过在`/etc/sysctl.conf`文件中添加一行实现：

```shell
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```
