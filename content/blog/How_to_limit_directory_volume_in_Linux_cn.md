Title: 如何限制Linux中文件夹的大小
Date: 2021-2-7 11:00
Category: Linux
Tags: Linux, Server
Slug: blog/limit_directory_volume
Author: BobAnkh
Summary: 使用镜像文件挂载的形式来实现限制Linux中文件夹的大小
Illustration: background.jpg

[TOC]

> 通过挂载镜像文件来限制文件夹大小，不需要时简单卸载即可

## 挂载

1. 首先创建指定大小的磁盘镜像文件

    ```shell
    dd if=/dev/zero of=/root/disk.img bs=2M count=512
    ```

    > 输入设备使用的是可以提供无限字符的/dev/zero，而这里创建的是大小为1GB的磁盘镜像

2. 将镜像文件挂载为设备

    ```shell
    losetup /dev/loop0 /root/disk.img
    ```

    > 如果loop0已经使用了也可以用loop1, loop2等

3. 格式化设备

    ```shell
    mkfs.ext3 /dev/loop0
    ```

4. 挂载为文件夹

    ```shell
    mount -t ext3 /dev/loop0 /mnt/disk1
    ```

    > 这样就限制/mnt/disk1这个文件夹只能使用1GB大小的空间了

## 卸载

1. 先卸载文件夹

   ```shell
   umount /mnt/disk1
   ```

2. 再卸载设备

   ```shell
   losetup -d /dev/loop0
   ```

3. 如不再需要可直接删除镜像文件

   ```shell
   rm -f /root/disk.img
   ```
