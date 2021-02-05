Title: Configure zsh under Ubuntu
Date: 2021-2-5 14:00
Category: Linux
Tags: Linux, Ubuntu, zsh, shell, Download
Slug: blog/lychee
Author: BobAnkh
Summary: 在Linux下手动配置zsh
Illustration: background.jpg

[TOC]

> 大部分现成的zsh配置方案都是使用了oh-my-zsh，但是其拖慢了zsh的速度，降低了命令行的效率，因而我选择自己安装并配置zsh

## 下载内容进行安装

### 安装zsh：

```shell
sudo apt-get install zsh -y
```

### 下载插件和主题：

> 这里都选择了手动下载，不过也可以选用使用apt下载

```shell
mkdir .zsh
git clone --depth=1 https://github.com/zsh-users/zsh-syntax-highlighting ~/.zsh/zsh-syntax-highlighting
git clone --depth=1 https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/.zsh/powerlevel10k
```

## 更改默认shell

> 选用apt下载是对应的路径应为如`/usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh`

```shell
chsh -s /usr/bin/zsh
```

此时可以注销并重新登录，然后就会自定义配置zsh

配置好后会自动生成`.zshrc`文件，我们将用其来启用插件和主题

## 添加需要的插件和主题：

```shell
echo 'source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh' >>~/.zshrc
echo 'source ~/.zsh/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh' >>~/.zshrc
echo 'source ~/.zsh/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
source ~/.zsh
```

第一次使用powerlevel10k主题时，会触发配置界面，根据提示和自己的喜好自行配置即可

## 额外说明

配置过一次之后，只需要将`.zshrc`文件和`.p10k.zsh`文件都保存下来，今后只需要在下载好对应内容后将这两个配置文件拷贝入用户目录中即可

部分内容也需要进入这两个配置文件进行修改，根据注释可以自行完成一些私人定制化的操作
