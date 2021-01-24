Title: 搭建Nextcloud私人网盘
Date: 2020-10-16 23:30
Category: Linux
Tags: Linux, Ubuntu, Cloud-Storage, Server, Nextcloud
Slug: blog/nextcloud_all_by_hand
Author: BobAnkh
Summary: 纯手动搭建Nextcloud私人网盘
Illustration: background.jpg

[TOC]

> 注意：作为root用户进行工作或在对应命令前加上sudo
>
> 注意：部分命令可能根据安装版本不同需要略微调整

## 基本部分

### 1. 输入以下指令安装相关内容

```console
apt-add-repository ppa:ondrej/php && apt update -y && apt upgrade -y
apt install apache2 -y
service apache2 start
apt install php7.3 libapache2-mod-php7.3 php7.3-mbstring -y
apt install php7.3-zip php7.3-dom php7.3-xml php7.3-gd php7.3-curl -y
service apache2 restart

#test
echo "<?php phpinfo() ?>" > /var/www/html/phpinfo.php
#此时访问ip可以查看页面
```

目前设计的目录结构为:

```text
#ideal structure
/cloudserver
.
|--data
|--log
`--nextcloud
```

### 2. 修改apache2相关配置

```console
vim /etc/apache2/sites-enabled/000-default.conf
```

内容如下自行修改：

```text
<VirtualHost *:80>
修改为：
<VirtualHost 你的域名:80>

#ServerName www.example.com
修改为
ServerName 你的域名

ServerAdmin webmaster@localhost
DocumentRoot /var/www/html
修改为
ServerAdmin 你的邮箱地址
DocumentRoot /cloudserver/nextcloud

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
修改为
ErrorLog /cloudserver/log/error.log
CustomLog /cloudserver/log/access.log combined
```

最后再修改一下apache2.conf，文件位于/etc/apache2/apache2.conf，不修改的话会403报错

```console
vim /etc/apache2/apache2.conf
```

```text
<Directory /var/www>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
修改为
<Directory /cloudserver>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```

然后启动apache2:

```console
service apache2 start
```

### 3. 处理数据库

```console
apt install mysql-server -y
apt install php7.3-mysql -y

#登录mysql
mysql -u root -p

#创建名为nextcloud的数据库
mysql> CREATE DATABASE nextcloud;

#切换数据库
mysql> USE nextcloud;

#创建名为nextcloud的用户，密码为password，并赋予相关权限
#mysql8.0之前也可使用下述语句，而8.0之后使用次语句会出现授权问题
#mysql> GRANT All  ON nextcloud.* TO nextcloud@localhost IDENTIFIED BY 'password';
mysql> CREATE USER nextcloud@localhost IDENTIFIED WITH mysql_native_password BY 'password';
mysql> GRANT All  ON nextcloud.* TO nextcloud@localhost;

#登出mysql
mysql> exit
```

### 4. 正式安装nextcloud

```console
cd /cloudserver/nextcloud
rm -rf *
wget https://download.nextcloud.com/server/releases/nextcloud-18.0.4.zip
apt install unzip -y
unzip nextcloud-18.0.4.zip
rm nextcloud-18.0.4.zip
mv nextcloud/* ../nextcloud/
rm -rf nextcloud/
```

新建一个脚本:

```console
vim set.sh
```

复制以下内容并保存:

```bash
#!/bin/bash
ocpath='/cloudserver/nextcloud'
htuser='www-data'
htgroup='www-data'
rootuser='root'
printf "Creating possible missing Directories\n"
mkdir -p $ocpath/data
mkdir -p $ocpath/assets
mkdir -p $ocpath/updater

printf "chmod Files and Directories\n"
find ${ocpath}/ -type f -print0 | xargs -0 chmod 0640
find ${ocpath}/ -type d -print0 | xargs -0 chmod 0750

printf "chown Directories\n"
chown -R ${rootuser}:${htgroup} ${ocpath}/
chown -R ${htuser}:${htgroup} ${ocpath}/apps/
chown -R ${htuser}:${htgroup} ${ocpath}/assets/
chown -R ${htuser}:${htgroup} ${ocpath}/config/
chown -R ${htuser}:${htgroup} ${ocpath}/data/
chown -R ${htuser}:${htgroup} ${ocpath}/themes/
chown -R ${htuser}:${htgroup} ${ocpath}/updater/

chmod +x ${ocpath}/occ
printf "chmod/chown .htaccess\n"
if [ -f ${ocpath}/.htaccess ]
  then
  chmod 0644 ${ocpath}/.htaccess
  chown ${rootuser}:${htgroup} ${ocpath}/.htaccess
fi
if [ -f ${ocpath}/data/.htaccess ]
 then
  chmod 0644 ${ocpath}/data/.htaccess
  chown ${rootuser}:${htgroup} ${ocpath}/data/.htaccess
fi
```

执行上述脚本:

```console
chmod +x set.sh
sh set.sh

chmod -Rf 770 /cloudserver/data/
chown -Rf www-data:www-data /cloudserver/data/

service apache2 restart
```

访问web进行配置（也可以先不访问，后续再配置）。这里其实是最简单但也很有可能出错的地方，主要是两个地方：数据目录和数据库配置。数据目录要填写绝对目录，最后不带“/”，而且要保证这个目录至少拥有750权限、用户名和组为www-data

### 5. 进行SSL部署

```console
#LET'S ENCRYPT
apt install certbot python3-certbot-apache -y
certbot --apache
service apache2 restart
```

刚才不登录这里就不用改了

```console
vim /cloudserver/nextcloud/config/config.php
```

修改:

```text
'overwrite.cli.url' => 'http:yoursite'
change to 'overwrite.cli.url' => 'https:yoursite'
```

继续配置SSL:

```console
vim /etc/apache2/sites-enabled/000-default-le-ssl.conf
```

```text
在第一行加入
LoadModule headers_module /usr/lib/apache2/modules/mod_headers.so
在<VirtualHost 你的域名>和</VirtualHost>之间加入
<IfModule mod_headers.c>
      Header always set Strict-Transport-Security "max-age=15552000; includeSubDomains; preload"
</IfModule>
```

重启web服务器:

```console
service apache2 restart
```

此时全部内容已安装完成，访问web端填写对应信息即可

## 进阶部分

### 1. 可以配置内存缓存

```console
apt install php-apcu memcached php-memcached -y
```

在nextcloud/config/config.php中加入以下内容:

```php
'memcache.local' => '\OC\Memcache\APCu',
  'memcache.distributed' => '\OC\Memcache\Memcached',
  'memcached_servers' => array(
     array('localhost', 11211),
     ),
```

一个完整的config.php文件示例:

```php
<?php
$CONFIG = array (
  'instanceid' => 'ocwFEEGDExfd9',
  'passwordsalt' => 'ajfjrgahughurhgodsi',
  'secret' => 'N7CzQTEXU5Fefefdf/xaf+3afrggdrtGRergsGVlLPgQzlnVm',
  'trusted_domains' =>
  array (
    0 => 'cloud.nosu.win',
  ),
  'datadirectory' => '/cloudserver/data',
  'overwrite.cli.url' => 'http://cloud.nosu.win',
  'dbtype' => 'mysql',
  'version' => '11.0.2.7',
  'dbname' => 'db',
  'dbhost' => 'localhost',
  'dbport' => '',
  'dbtableprefix' => 'db_',
  'dbuser' => 'db',
  'dbpassword' => 'dbpasswd',
  'logtimezone' => 'UTC',
  'installed' => true,
  'memcache.local' => '\OC\Memcache\APCu',
  'memcache.distributed' => '\OC\Memcache\Memcached',
  'memcached_servers' => array(
     array('localhost', 11211),
     ),
);
```

### 2. 访问Nextcloud时，url中会含有index.php，可以去除

在config.php中加入:

```php
'htaccess.RewriteBase' => '/',
```

执行

```console
sudo -u www-data php occ maintenance:update:htaccess
```
