Title: Install Transmission under Ubuntu
Date: 2020-6-16 23:40
Category: Linux, Server
Tags: Linux, Ubuntu, Transmission, Server, Download
Slug: blog/transmission
Author: BobAnkh
Summary: 在Linux下安装一个快速免费且简单的BitTorrent客户端Transmission，并启用WebUI

[TOC]

> 注意：作为root进行工作

## 1. 安装 transmission-daemon

只需要在你的终端输入以下命令即可完成安装:

```shell
apt install transmission-daemon -y
```

## 2. 修改 `settings.json`

> 首先你需要先停止 `transmission-daemon` 然后在修改配置文件
> 配置文件 `settings.json` 通常会在目录 `/etc/transmission/`之下

在你的终端中输入以下两行命令来停止`transmission-daemon`并打开配置文件 `settings.json`

```shell
service transmission-daemon stop
vim /etc/transmission-daemon/settings.json
```

这里提供了一个配置文件 `settings.json`可行的示例

> 其中，备注为`check`的行需要你进行检查是否与示例一致，备注为`modify`的行你可以根据自己的情况自行修改：

```json
{
    "alt-speed-down": 50,
    "alt-speed-enabled": false,
    "alt-speed-time-begin": 540,
    "alt-speed-time-day": 127,
    "alt-speed-time-enabled": false,
    "alt-speed-time-end": 1020,
    "alt-speed-up": 50,
    "bind-address-ipv4": "0.0.0.0",  //check
    "bind-address-ipv6": "::", //check
    "blocklist-enabled": false,//check
    "blocklist-url": "http://www.example.com/blocklist",
    "cache-size-mb": 4,
    "dht-enabled": true,
    "download-dir": "/var/lib/transmission-daemon/downloads", //can modify to where you want to put your files
    "download-queue-enabled": true,
    "download-queue-size": 5,
    "encryption": 1,
    "idle-seeding-limit": 30,
    "idle-seeding-limit-enabled": false,
    "incomplete-dir": "/var/lib/transmission-daemon/Downloads", //can modify to where you want to put your files
    "incomplete-dir-enabled": false,
    "lpd-enabled": false,
    "message-level": 1,
    "peer-congestion-algorithm": "",
    "peer-id-ttl-hours": 6,
    "peer-limit-global": 200,
    "peer-limit-per-torrent": 50,
    "peer-port": 51413,
    "peer-port-random-high": 65535,
    "peer-port-random-low": 49152,
    "peer-port-random-on-start": false,
    "peer-socket-tos": "default",
    "pex-enabled": true,
    "port-forwarding-enabled": true,
    "preallocation": 1,
    "prefetch-enabled": 1,
    "queue-stalled-enabled": true,
    "queue-stalled-minutes": 30,
    "ratio-limit": 2,
    "ratio-limit-enabled": false,
    "rename-partial-files": true,
    "rpc-authentication-required": true,//check
"rpc-bind-address": "0.0.0.0", //check
    "rpc-enabled": true, //check
    "rpc-host-whitelist": "",//check
    "rpc-host-whitelist-enabled": true,//check
    "rpc-password": "passwd",//change to your password
    "rpc-port": 9091,//port to visit from web
    "rpc-url": "/transmission/",//can modify
    "rpc-username": "transmission", //user
    "rpc-whitelist": "*.*.*.*",//modify
    "rpc-whitelist-enabled": true,//check
    "scrape-paused-torrents-enabled": true,
    "script-torrent-done-enabled": false,
    "script-torrent-done-filename": "",
    "seed-queue-enabled": false,
    "seed-queue-size": 10,
    "speed-limit-down": 100,
    "speed-limit-down-enabled": false,
    "speed-limit-up": 100,
    "speed-limit-up-enabled": false,
    "start-added-torrents": true,
    "trash-original-torrent-files": false,
    "umask": 18,
    "upload-slots-per-torrent": 14,
    "utp-enabled": true
}
```

完成修改后，保存并退出，然后在终端输入以下命令来启动`transmission-daemon`并检查其状态

```shell
service transmission-daemon start
service transmission-daemon status
```

> 如果此时没有输出任何warning或者failure，那么说明配置完成并且可以正常工作了

然后你就可以访问以下地址`http://<your ip address>:9091`进入到`transmission-daemon`的WebUI界面进行管理

## 3. 在配置并启动后你可能会遇到的问题

### “UDP Failed to set receive buffer ... ”error

只需要输入以下两个命令即可解决:

```shell
sysctl -w net.core.rmem_max=8388608
sysctl -w net.core.wmem_max=8388608
```
