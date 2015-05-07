
---

layout: post
title: 那些年折腾过的Linux ------Arch Linux
categories: [Linux]
tags: [Linux]
description:
fullview: true

---

##那些年折腾过的Linux ------Arch Linux


好几番折腾arch ！现在终于搞得差不多了！前面有n多次失败！从基本系统安装，桌面配置！compiz配置 ～～等等 遇到各种各样的问题～～现在终于有点“成就感”了（见笑了,本人菜鸟都算不上，请高手们不笑话我！！！）简单的庆祝一下！！！哈哈！！废话不说了！作作笔记吧！！记录一下我的足迹！！
      很早就知道arch这个系统了，一直没敢装！（不信自己的实力～～～）arch在网络上口碑很好!以“速度快”，“保持最新”闻名！！等等~~

一：基本系统安装（略过）

二：基本配置

     1，联网（client也省略）

     2，更新系统 sudo pacman -Syu （同步与更新）包括改源，/etc/pacman.d/menu.lst

     3，建一个新用户待用 ，useradd   -m teze        passwd teze （密码）

        root运行visudo 加teze all =(all)
     4,   安装xorg   #sudo pacman -S xorg （可以试着配置xorg#sudo Xorg -configure）

     5,安装显卡驱动，#sudo pacman -S xf86-video-ati 或者catalyst （包括依赖）

      6,再一次配置xorg #sudo Xorg -configure      生产文件/root/xorg.conf.new(可对它编辑)

      7，检查xorg.conf.new 是否有错，如果没错则复制到/etc/X11/xorg.conf

     8,安装桌面管理器：gnome ，或者kde系列，或者xfce4，等等

     9，我安装gnome 因为熟悉环境 ，我之前安装的kde ，xfce4等 遇到很多问题，怕了

      10，#sudo pacman -S gnome

      11，启动hal。 关键地方，刚开始因为没这一操作一直进不了桌面，郁闷了好久!!!!!!!

             #sudo /etc/rc.d/hal      restart    (然后将hal 加入到、/etc/rc.conf 的DEMOUDE中 开机自启动)

      12，可以进桌面了 #startx

三，基本环境配置

     1，设置字体 安装字体      $ sudo pacman Sy ttf ｜less （从安装包列表中查找可用的字体包） 
         $ sudo pacman S sdl_ttf font-bh-ttf ttf-dejavu ttf-fireflysung ttf-ms-fonts

                   当前的开源字体已经相当不错了，尤其是文泉驿字体。
                     安装文泉驿点阵宋体和正黑体。当前已经进入Community 了。
       $ tupac -S wqy-bitmapfont wqy-zenhei
       $ sudo vi /etc/X11/xorg.conf ，增加字体路径 
                    如果没有安装 fontconfig ，就用 pacman 安装一下，然后，
                     下载本文附件：fonts.conf.tar.gz ，并解压到 $HOME下：
       $ tar zxf fonts.conf.tar.gz -C ~/
                      这会在 $HOME下产生一个 .fonts.conf 文件，可以优化中文显示。

                      如果磁盘上有Windows系统，也可以：
       $ sudo mkdir /usr/share/fonts/msfonts 
                      复制一些Windows 字体到此目录下，如：msyh, simhei, simyou 
                       并自行编辑.fonts.conf 文件。当然了，最好不要使用Windows字体，因为是有版权的。

      2，设置locale 中文化，编辑/etc/rc.conf 的locale=zh-CN:UTF8

            编辑/etc/locale.gen 注销zh_CNxx前的#号

            可用命令#locale 查看当前用的locale

              #locale-gen 从新写入locale设置！

           从启就是中文了，前提是你有安装中文字体！！前面有安装 最好先安装字体 再改locale

    3.安装输入法

       SCIM

       $ sudo pacman -S scim-tables   (或安装scim-fcitx。要用拼音，安装scim-pinying)
        $ cat >>~/.profile <<EOF 
            > LC_CTYPE="zh_CN.utf8" 
            > export XMODIFIERS=@im=SCIM
             > export GTK_IM_MODULE="scim"
            > export QT_IM_MODULE="scim"
             > scim -f socket -c socket -d
            > EOF

接下去就是安装基本软件了（未完待续~~~~~）
