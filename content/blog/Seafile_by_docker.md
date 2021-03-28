Title: 在Ubuntu下使用Docker搭建Seafile网盘
Date: 2021-2-7 12:00
Category: Linux
Tags: Linux, Ubuntu, Cloud-Storage, Server, Seafile, Docker
Slug: blog/seafile_by_docker
Author: BobAnkh
Summary: 在Ubuntu下使用Docker搭建Seafile网盘，并使用docker-compose进行编排
Illustration: background.jpg

[TOC]

> 默认以root用户进行安装
>
> 一键安装bash脚本可见[install-docker-dockercompose.sh](https://github.com/BobAnkh/blog/blob/master/assets/install-docker-dockercompose.sh)，自行下载并谨慎使用，如有问题，概不负责

## 安装Docker

```shell
sudo apt update -y
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release -y
```

添加gpg秘钥并检查

```shell
# 官方命令如下，国内安装可使用镜像源
# curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

添加仓库

```shell
# 官方仓库如下，国内安装可使用镜像
# echo \
#   "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/ \
#   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```shell
sudo apt update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

运行以下命令来检查Docker是否安装成功

```shell
sudo docker --version
sudo docker run hello-world
```

若除docker版本外，能够输出以下内容，则说明Docker已经安装成功：

```console
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
0e03bdcc26d7: Pull complete
Digest: sha256:31b9c7d48790f0d8c50ab433d9c3b7e17666d6993084c002c2ff1ca09b96391d
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

若想以非root用户不带sudo地来运行docker，可以使用下述命令：

```shell
sudo usermod -aG docker $USER
```

## 安装docker-compose

> 使用docker-compose来编排会更为容易一些

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo docker-compose --version
```

## 编写docker-compose.yml

> 以下内容参考[官方文档](https://cloud.seafile.com/published/seafile-manual-cn/docker/%E7%94%A8Docker%E9%83%A8%E7%BD%B2Seafile.md)进行编写，默认具有域名并且使用letsencrypt申请ssl证书(也可自行申请，详情请查阅官方文档)

```yaml
version: '2.0'
services:
  db:
    image: mariadb:10.5
    container_name: seafile-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=Your_Password # 需要, 设置MySQL的root密码.
      - MYSQL_LOG_CONSOLE=true
    volumes:
      - /opt/seafile-mysql/db:/var/lib/mysql  # 需要, 持续化挂载MySQL数据的路径.
    networks:
      - seafile-net

  memcached:
    image: memcached:1.5.6
    container_name: seafile-memcached
    entrypoint: memcached -m 256
    networks:
      - seafile-net

  seafile:
    image: seafileltd/seafile-mc:latest
    container_name: seafile
    ports:
      - "80:80"
      - "443:443"  # 启用https必须配置此项.
    volumes:
      - /opt/seafile-data:/shared   # 需要, 持续化挂载Seafile数据的路径.
    environment:
      - DB_HOST=db
      - DB_ROOT_PASSWD=Your_Password  # 需要, 即MySQL的root密码.
      - TIME_ZONE=Asia/Shanghai # 可选, 默认为 UTC. 需要设置为你的本地时区.
      - SEAFILE_ADMIN_EMAIL=me@example.com # 管理员用户登录用户名, 默认值为 'me@example.com'.
      - SEAFILE_ADMIN_PASSWORD=asecret     # 管理员用户密码, 默认值为 'asecret'.
      - SEAFILE_SERVER_LETSENCRYPT=true   # 如要使用letencrypt来签发ssl则须改为true
      - SEAFILE_SERVER_HOSTNAME=docs.example.com # 你的域名，前提是已成功解析
    depends_on:
      - db
      - memcached
    networks:
      - seafile-net

networks:
  seafile-net:
```

在docker-compose.yml的同级目录下运行`docker-compose up -d`即可启动，等待片刻若无问题则可访问对应域名进行相关配置等。

可以使用`docker ps`查看容器运行情况，使用`docker logs seafile`查看seafile主容器输出日志。

## 另附一些Docker使用的技巧：

### 列出所有镜像的ID:

```shell
docker ps -aq
```

### 停止所有容器

```shell
docker stop $(docker ps -aq)
```

### 删除所有容器

```shell
docker rm $(docker ps -aq)
```

也可以使用`docker container prune -f`删除所有停止的容器

### 删除所有镜像

```shell
docker rmi $(docker images -q)
```

也可以使用`docker image prune --force --all`或`docker image prune -f -a`删除所有不使用的镜像
