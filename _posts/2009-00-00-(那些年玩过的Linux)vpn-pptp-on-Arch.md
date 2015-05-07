
---

layout: post
title: vpn pptp on Arch
categories: [Linux]
tags: [Linux]
description:
fullview: true

---

_Edit by Fooyou  <foyou23@qq.com>_  
	
	2009/2/20 13:06:58 
	
##Arch 下安装vpn pptp 服务器


本人菜鸟　想在`archlinux`下实现vpn代理服务器　
本人网络环境：`校园网，dr.com 客户端８０２.１x认证`

### 安装pptpd：＃sudo pacman -S pptpd ppp
配置文件如下：

 - 第一个服务器Demo：/etc/pptpd.conf 内容如下：

    !# Read man pptpd.conf and write your pptpd configuration here
    option /etc/ppp/options.pptpd
    /var/log/messages.log     #将调试信息写入
    localip 172.16.0.1       //本地ip？服务器的ip（网关）设置VPN服务器本地的地址
    remoteip 172.16.0.10-30　 //　客户端的ip地址池

 - 第二个选项参数：/ect/ppp/options.pptpd　内容如下：

    lock
    auth         　　　
    name pptpd        
    ＃refuse-pap
    refuse-eap
    refuse-chap
    refuse-mschap
    ＃repuire-mschap-v2
    require-mppe-128
    ＃proxyarp
    nobsdcomp
    nodeflate
    ＃novj
    ＃novjccomp
    Ms-dns   218.193.32.1
    Ms-dns   218.193.32.1
    配置说明：
    name IsMole-VPN ———— pptpd server 的名称。
    refuse-pap ———— 拒绝 pap 身份验证模式。
    refuse-chap ———— 拒绝 chap 身份验证模式。
    refuse-mschap ———— 拒绝 mschap 身份验证模式。
    require-mschap-v2 ———— 在端点进行连接握手时需要使用微软的 mschap-v2 进行自身验证。
    require-mppe-128 ———— MPPE 模块使用 128 位加密。
    ms-dns 202.106.46.151
    ms-dns 202.106.0.20 ———— ppp 为 Windows 客户端提供 DNS 服务器 IP 地址，第一个 ms-dns 为 DNS Master，第二个为 DNS Slave。
    proxyarp ———— 建立 ARP 代理键值。
    debug ———— 开启调试模式，相关信息同样记录在 /var/logs/message 中。
    lock ———— 锁定客户端 PTY 设备文件。
    nobsdcomp ———— 禁用 BSD 压缩模式。
    novj
    novjccomp ———— 禁用 Van Jacobson 压缩模式。
    nologfd ———— 禁止将错误信息记录到标准错误输出设备(stderr)。

 - 最后一个文件添加客户端帐号:/etc/ppp/chap-secrets  内容如下：

    ＃ Secrets for authentication using CHAP
    ＃client     server     secret             IP addresses
    teze         pptpd     1234567     *   
    //  用户　　deamon 服务器　　　密码　　　分配的ip地址＊代表任意

**以上３个文件基本可以满足基本需求了**
### 注意事项：
1. 连接上了，不能上网，可能的原因：启动内核转发数据包

    ＃sudo sysctl net.ipv4.ip_forward=1
    写入配置文件，每次启动都生效
    ＃sudo vim /etc/sysctl.conf   加入如下内容，更改 ：     
        net.ipv4.ip_forward=1
    保存退出，并执行下面的命令来生效它：# sysctl -p

２.  配置防火墙(iptables)使用NAT

    在这个例子里，服务器接入 Internet 的网络接口是 eth0，需要根据自己的实际需要修改。
    ＃sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
    写入脚本，每次启动都生效。
    ＃sudo vim /etc/rc.local
    加入：/sbin/iptables   -t nat -A POSTROUTING -o eth0 -j MASQUERADE　
    　　　exit 0

３.配置防火墙(iptables) 放行 VPN 数据包

    PPTP 使用 1723 端口和编号为47的IP协议（GRE常规路由封装)我们需要让防火墙里放行。#sudo iptables -A INPUT -p tcp --dport 1723 -j ACCEPT
    ＃sudo iptables -A INPUT -p gre -j ACCEPT
    最后重启iptables：
    ＃ sudo /etc/rc.d/iptables restart

4.windows客户端连接错误提示，及可能的解决方案（本人猜想）

    4.1 连接时候提示：742错误 远程服务器不支持加密　好像应该在
    
        linux下的
         /ect/ppp/options.pptpd加入require-mppe-128　
    
    4.2 连接时候提示：800错误　
    　 可能的原因：在／etc／ppp／options.pptpd中　#repuire-mschap-v2　注销掉　本人是这样  
    4.3 几个检测小方法：如果不能连接　试试互相ping对方ip　检测网络通不通　
    　   或者试试　telnet　对方ip　加端口１７２３（好像是vpn服务端口）
    4.4如果出现错误619则输入命令
       ＃mknod /dev/ppp c 108 0
    注意：如果虚拟机内核不支持MPPE的话，无法使用加密，用WINDOWS默认VPN连接会显示“证书信任错误”。
    解决方法：修改/etc/ppp/options.pptpd注释掉require-mppe-128这行，然后windows的vpn拨号的属性改为可选加密，再次连接就成功了。

5.设置开机自动运行服务。

    ＃chkconfig pptpd on
    ＃chkconfig iptables on
    另外你需要图形化管理VPN的话，可以用Webmin
    最后客户端建立vpn连接方法：这里省略　很简单百度一下
    开始配置 PPTP VPN
    编辑 /etc/pptp.conf
    listen 0.0.0.0debuglocalip 192.168.70.1remoteip 192.168.70.200-238
    编辑 /etc/ppp/options
    name pptpdlockdebug+chapms-dns 8.8.4.4 # primary DNS server ip addressms-dns 8.8.8.8 # secondary DNS server ip address
    编辑 /etc/ppp/chap-secrets ，加入用户
    username pptpd password *
    启动 pptpd
    /etc/rc.d/pptpd start
    安装 iptables
    pacman -S iptables
    启动 iptables
    /etc/rc.d/iptables start
    添加 iptables 规则
    iptables -A INPUT -p tcp --dport 1723 -j ACCEPTiptables -A INPUT -p 47 -j ACCEPTiptables -A FORWARD -i ppp+ -o vent0 -j ACCEPTiptables -t nat -A POSTROUTING -s 192.168.70.0/24 -j SNAT --to-source 外网IP
    保存 ipables 规则
    /etc/rc.d/iptables save
    在 /etc/sysctl.conf 下添加 ，开启转发
    net.ipv4.ip_forward=1
    在 /etc/rc.conf 中的 DAEMONS 一栏中添加 iptables 及 pptpd 启动项
    DAEMONS=( iptables pptpd bftpd mysqld php-fpm nginx vnstat syslog-ng network crond sshd "vzquota" )

最后重启服务器