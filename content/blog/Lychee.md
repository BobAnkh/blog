Title: Install Lychee under Ubuntu
Date: 2020-6-19 22:00
Category: Linux
Tags: Linux, Ubuntu, Lychee, Server, Download
Slug: blog/lychee
Author: BobAnkh
Summary: 在Linux下（本文以Ubuntu 18.04 LTS为例）搭建一个美观简约的画廊
Illustration: background.jpg

[TOC]

> 注意：作为root用户进行工作或在对应命令前加上sudo
>
> 本次搭建使用的是LAMP组合（即Linux，Apache2，Mysql，Php），关于Apache2和Mysql的对应操作可以参见Nextcloud网盘搭建的对应部分

## 1. 安装php依赖 (如果你需要上传视频，那么你还需要安装ffmpeg和php7.3-ffmpeg)

首先使用如下命令，将对应存储库添加到系统查找软件的位置列表中：

```shell
apt-add-repository ppa:ondrej/php && apt update -y && apt upgrade -y
```

接下来就是安装相关依赖了：

```shell
apt install php7.3 php7.3-common php7.3-cli php7.3-cgi php7.3-fpm php7.3-gd php7.3-mysql php7.3-sqlite3 php7.3-pgsql php7.3-opcache php7.3-mbstring php7.3-curl php7.3-xml php7.3-xmlrpc php7.3-zip php7.3-intl php7.3-json php7.3-bz2 php7.3-imagick php7.3-ldap -y
```

## 2. 使用vim等打开配置文件/etc/php/7.3/apache2/php.ini

```ini
max_execution_time = 200
post_max_size = 100M
upload_max_filesize = 20M  # 如果你需要添加视频，请将这个增加到合适大小
memory_limit = 256M
```

## 3. 安装Lychee

```shell
apt -y install git
cd /opt/wwwroot
git clone --recurse-submodules https://github.com/LycheeOrg/Lychee.git
```

## 4. 安装composer

```shell
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

## 5. 进入到Lychee的目录下运行依赖安装

```shell
cd Lychee
composer install --no-dev
```

## 6. 为上传设置存储的许可

```shell
sudo chgrp www-data -R storage public/dist public/sym public/uploads
sudo chmod -R 775 storage/* app/* public/dist public/sym public/uploads bootstrap/
sudo chmod 775 .
# Alternatively, set ownership to your web user with
sudo chown -R www-data:www-data .
```

## 7. 配置 .env 文件

```shell
cp .env.example .env
```

编辑对应内容为如下：

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
DB_DROP_CLEAR_TABLES_ON_ROLLBACK=false

# leave empty or delete the line for default (empty string).
DB_OLD_LYCHEE_PREFIX=
# DB_OLD_LYCHEE_PREFIX=someprefix_
```

## 8. 进行数据库的迁移

```shell
php artisan migrate
```

## 9.  生成应用秘钥

```shell
php artisan key:generate
```

## 10. 打开对应网址，欣赏自己搭建的画廊吧

![Homepage](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/lychee/b68eaabdc7843.png)

![Darksouls Album](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/lychee/8d04af3c83d6d.png)

![Detailed info](https://cdn.jsdelivr.net/gh/BobAnkh/blog/figures/lychee/ae12b1f941b16.png)
