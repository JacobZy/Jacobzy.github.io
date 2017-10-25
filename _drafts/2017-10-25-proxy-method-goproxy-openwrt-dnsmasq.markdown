---
layout:     post
title:      "GOPROXY-树莓派-openwrt-dnsmasq配置"
subtitle:   "proxy"
date:       2017-10-25 7:00:00
author:     "Jacob"
header-img: "img/postbg/post-bg-2015.jpg"
header-mask: 0.3
catalog:    true
tags:
    - web
---



## goproxy
最早是叫goagent，利用google的引擎。类似的有XX-NET

linux上为了使chrome也可以用必须导入安全证书

$ mv ~/.pki/nssdb ~/.pki/nssdb.corrupted
$ mkdir ~/.pki/nssdb
$ chmod 700 ~/.pki/nssdb
$ certutil -d sql:$HOME/.pki/nssdb -N

Check if working alright

$ certutil -d sql:$HOME/.pki/nssdb -L
Certificate Nickname                                         Trust Attributes
                                                             SSL,S/MIME,JAR/XPI

Import certificate

$ certutil -d sql:$HOME/.pki/nssdb -A -t "C,," -n GoAgent -i ~/programs/goagent/local/CA.crt

自启动

nohup ./opt/goproxy/goproxy>/dev/null 2>&1 &


## 树莓派安装openwrt
树莓派作为路由器，安装openwrt，而且可以申请HENET的IPV6隧道，配置自动更新本地IP到HENET。利用openwrt上的dnsmasq，更新HOST为google IPV6,使用<https://github.com/lennylxx/ipv6-hosts>。这样一来，通过此树莓派路由器上网即可登录GMAIL、YOUTUBE等

1. router  是否公网IP
2. 安装OPENWRT
3. 配置DNSMASQ
4. 配置HE tunnel

1. 网址配置
连电脑修改192.168.1.1 以DHCP，连接到route上网
uci set network.lan.proto=dhcp
uci commit
/etc/init.d/network restart

In order to set it up with a fixed IP different from the default 192.168.1.1 do the following:

uci set network.lan.ipaddr=192.168.1.199
uci commit
/etc/init.d/network restart

1. update install 问题
opkg update && opkg install luci

Open /etc/opkg.conf

delete the line that reads option check_signature 1

Try opkg update again and the problem should be fixed.

修改 /etc/opkg/下的源配置地址为正确，即generic目录下

2.正确安装usb ethernet驱动模块，加密模块

lsusb  无线网卡芯片型号为

1.首先安装USB设备支持：

opkg update && opkg  kmod-usb-core kmod-usb-uhci kmod-usb-ohci kmod-usb2

Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 004: ID 04f2:b594 Chicony Electronics Co., Ltd 
Bus 001 Device 003: ID 0cf3:e500 Atheros Communications, Inc. 
Bus 001 Device 005: ID 0b95:772a ASIX Electronics Corp. AX88772A Fast Ethernet
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

2.安装无线网卡驱动(这里根据实际的网卡选择驱动)：
Kernel modules -> USB Support -> kmod-usb-net
kmod-usb-net-asix
asix-ax88179

Then search for mcs7830 in the Find Package field, and install the package called kmod-usb-net-mcs7830


opkg install kmod-rtl8192cu

重启一下树莓派， 检查驱动是否加载成功：

root@Raspberry:~# lsmod | grep 8192
compat                  1919  4 rtl8192cu
mac80211              354713  3 rtl8192cu
rtl8192c_common        33465  1 rtl8192cu
rtl8192cu              59189  0
rtl_usb                 8011  1 rtl8192cu
rtlwifi                43783  2 rtl8192cu


->*kmod-usb-net-rtl8150

->*kmod-usb-net-rtl8152

-> *kmod-usb2


装USB网卡驱动：

opkg install kmod-usb-net-asix
安装无线网卡驱动：

opkg install kmod-rtl8192cu
让AP模式支持WPA加密：

opkg install hostapd hostapd-utils
重启树莓派，让驱动生效：

reboot

从菜单项“System->Software”可进入软件安装界面。点击“Update lists”按钮更新软件包列表，然后到“Avaible packages”寻找软件，安装即可。这里需要安装的是USB网卡驱动kmod-usb-net-asix，无线网卡驱动kmod-rtl8192cu，让AP模式支持WPA加密的两个软件包hostapd、hostapd-utils。

注意安装完需要重启树莓派，才能正确识别两个外接网卡。

3.添加wlan,hewan,  lan配置192.168.1.1

4.dnsmasq 配置，dnsmasq.d目录下添加配置文件

5.hosts添加google ipv6

6.安装goproxy

7.自启动goproxy

## dnsmasq、dnscrypt配置
导入google ipv6到HOST文件即可<https://github.com/lennylxx/ipv6-hosts>，并且可以选用更好的DNS

dnscrypt配置是为了更安全的DNS查询过程
nohup /opt/dnscrypt-proxy/x86_64-linux-musl/bin/dnscrypt-proxy /opt/dnscrypt-proxy/x86_64-linux-musl/etc/dnscrypt-proxy.conf > /dev/null 2>&1 &
/opt/dnscrypt-proxy/x86_64-linux-musl/bin/dnscrypt-proxy  -R bn-ca0 -a 127.0.0.1:35535
d0wn-nl-ns4  resolver   d0wn-us-ns4  107.181.168.52
sudo /etc/init.d/networking restart
dig youtube-ui.l.google.com @208.67.222.222 -p 443
dnscrypt-proxy --resolver-name=bikinhappy-sg  --local-address=127.0.0.1:35535  --tcp-only

## Genymotion安装QQ
1. virtualbox最新
2. 关闭BIOS的 SECURE BOOT，打开virtulize VCT
3. Genymotion 帐号建立 motorola motoX 4.3
4. 启动模拟手机，personal use
5. [TechBae.com]Genymotion-ARM-Translation_v1.1  安装
6. 设置中文local，安装谷歌中文输入法APK
7. 安装android QQ

genymotion中android与宿主机传递文件
1. virtualbox中设置共享目录，选择自动挂载。在android中映射于/mnt/share
2. 使用ES文件浏览器，设置工具中有root工具，打开其权限
3. 对于宿主机与android，共享目录都为可读写


## wine安装QQ
先从[WINEHQ](https://www.winehq.org)上得到匹配的安装版本，再进行安装，之后使用[winetricks-zh](https://github.com/hillwoodroc/winetricks-zh)安装QQ。

当然还可以安装virtualbox再安装微软提供的 virtual machines windows之后，进入虚拟机安装QQ，缺点是消耗内存。

## 电视盒子安装youtube
小米盒子 YOUTUBE TV使用：smart youtube 可以登录，不用安装google service。

使用goproxy 或者其它， 盒子安装证书 crt过程使用[my certificates](https://apkpure.com/my-certificates/com.wesbunton.projects.mycertificates)

