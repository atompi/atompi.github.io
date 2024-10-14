---
title: Ubuntu 16.04 Desktop 初始化
date: 2018/03/06
tags: Linux
description: Some old articles may have been deprecated.
---

安装完 Ubuntu 16.04 Desktop 后执行以下命令，会让你得到一个干净、完整的开发环境。

``` plain
# 选择阿里云源
sudo apt update
sudo apt upgrade -y
sudo reboot

sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt update
sudo apt install shadowsocks-qt5 -y
sudo apt install ssh
sudo apt install python-pip
sudo apt install curl
sudo pip install genpac
sudo apt install vim
sudo apt-get install exfat-utils

cd Documents/
mkdir autoproxy
cd autoproxy/
vim ~/Documents/autoproxy/user-rules.txt
vim ~/Documents/autoproxy/update.sh
# 配置 shadowsocks-qt5
cd ~/Documents/autoproxy/
bash update.sh
# 配置系统代理

sudo apt-get install privoxy -y
sudo mv /etc/privoxy/config /etc/privoxy/config.bak
sudo vim /etc/privoxy/config
sudo systemctl start privoxy
sudo systemctl enable privoxy
sudo vim /usr/local/bin/proxy
sudo chmod +x /usr/local/bin/proxy

sudo apt-get remove libreoffice*
sudo apt-get remove unity-webapps-common
sudo apt-get remove gnome-mahjongg gnome-sudoku gnome-mines
sudo apt remove aisleriot
sudo apt autoremove
sudo reboot

sudo dpkg -i google-chrome-stable_current_amd64.deb
# 安装 jdk
sudo update-alternatives --install /usr/bin/java java /usr/local/jdk1.8.0_152/bin/java 300
sudo update-alternatives --install /usr/bin/javac javac /usr/local/jdk1.8.0_152/bin/javac 300
sudo update-alternatives --install /usr/bin/jar jar /usr/local/jdk1.8.0_152/bin/jar 300
sudo update-alternatives --install /usr/bin/javah javah /usr/local/jdk1.8.0_152/bin/javah 300
sudo update-alternatives --install /usr/bin/javap javap /usr/local/jdk1.8.0_152/bin/javap 300
sudo update-alternatives --config java

sudo apt install zsh
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo proxy apt install git
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
sudo reboot

sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get -y update
sudo apt-get -y install docker-ce
sudo mkdir -p /etc/docker\nsudo tee /etc/docker/daemon.json &lt;&lt;-'EOF'\n{\n  "registry-mirrors": ["https://gfty7g09.mirror.aliyuncs.com"]\n}\nEOF\nsudo systemctl daemon-reload\nsudo systemctl restart docker
sudo gpasswd -a ${USER} docker

docker run -d -p 1337:1337 --restart=always --name=idealicsrv idealicsrv:1.0.0
sudo apt-get install -y cmake build-essential libreadline6-dev libncurses5-dev

docker pull redis:3.2.11
docker pull mysql:5.7.21
docker run -d -p 3306:3306 --name=mysql -e MYSQL_ROOT_PASSWORD=atompi123 mysql:5.7.21 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
docker run -d -p 6379:6379 --name=redis redis:3.2.11
sudo apt install libmysqlclient-dev

sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
sudo add-apt-repository 'deb http://typora.io linux/'
sudo apt-get update
sudo apt-get install typora
sudo add-apt-repository -r 'deb http://typora.io linux/'
sudo add-apt-repository -r 'ppa:hzwhuang/ss-qt5'

anaconda3/bin/conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
anaconda3/bin/conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
anaconda3/bin/conda config --set show_channel_urls yes
```
## ~/Documents/autoproxy/user-rules.txt

``` plain
! 用户自定义规则语法:
!
!   与gfwlist相同，使用AdBlock Plus过滤规则( http://adblockplus.org/en/filters )
!
!     1. 通配符支持，如 *.example.com/* 实际书写时可省略*为 .example.com/
!     2. 正则表达式支持，以\开始和结束， 如 \[\w]+:\/\/example.com\\
!     3. 例外规则 @@，如 @@*.example.com/* 满足@@后规则的地址不使用代理
!     4. 匹配地址开始和结尾 |，如 |http://example.com、example.com|分别表示以http://example.com开始和以example.com结束的地址
!     5. || 标记，如 ||example.com 则http://example.com、https://example.com、ftp://example.com等地址均满足条件
!     6. 注释 ! 如 ! Comment
!
!   配置自定义规则时需谨慎，尽量避免与gfwlist产生冲突，或将一些本不需要代理的网址添加到代理列表
!
!   规则优先级从高到底为: user-rule &gt; user-rule-from &gt; gfwlist
!
! Tip:
!   如果生成的是PAC文件，用户定义规则先于gfwlist规则被处理
!   因此可以在这里添加例外或常用网址规则，或能减少在访问这些网址进行查询的时间, 如下面的例子
!
!   但其它格式如wingy, dnsmasq则无此必要, 例外规则将被忽略, 所有规则将被排序
!

@@sina.com
@@163.com

twitter.com
youtube.com
||google.com
||wikipedia.org
```
## ~/Documents/autoproxy/update.sh

``` plain
#!/bin/bash
genpac --pac-proxy "SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" --gfwlist-url=https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt --output="autoproxy.pac" --user-rule-from="user-rules.txt"
```
## /etc/privoxy/config

``` plain
# 转发地址
forward-socks5   /               127.0.0.1:1080 .
# 监听地址
listen-address  localhost:8118
# local network do not use proxy
forward         192.168.*.*/     .
forward            10.*.*.*/     .
forward           127.*.*.*/     .
```
##

``` plain
#!/bin/bash
http_proxy=http://127.0.0.1:8118 https_proxy=http://127.0.0.1:8118 $*
```
## ~/.zshrc alias

``` plain
xinput set-prop $(xinput list|grep "AT Translated Set 2 keyboard"|awk '{print $7}'|cut -d '=' -f 2) "Device Enabled" 0

alias mysql="docker run -it --rm mysql:5.7.21 mysql $@"
alias redis-cli="docker run -it --rm redis:3.2.11 redis-cli $@"
```
