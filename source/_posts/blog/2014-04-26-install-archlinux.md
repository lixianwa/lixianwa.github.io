---
title: "安装archlinux"
date: 2014-04-26
categories: 技术
tags: ["archlinux"]
---

活着就是为了折腾，Ubuntu已经用了4年多了吧，从最近几年看来，ubuntu的发展目的是要面向大众化，由于要适合大众，所以他的操作更简单，图形界面更美观，这就导致它越来越笨重。对于一个追求速度，喜欢命令模式的程序猿，ubuntu已然不能满足我的要求，所以安装archlinux的想法产生了，活着不就是为了折腾嘛，于是一步一步定制专属自己的linux系统。


1.下载archlinux镜像文件
2.制作usb安装盘

         # dd bs=4M if=</archlinux-2014.04.01-dual.iso/的/路径> of=/dev/<U盘文件> && sync

3.从U盘启动选择Boot Arch Linux
4.配置安装
 1)连接网络
   有线连接：
   
         # dhcpcd
         
   无线连接：
   
         # wifi-menu
         
   ADSL 宽带连接：
   配置：
   
         # pppoe-setup
         
   连接：
   
         # systemctl start adsl
         
  2)选择软件源
  
         # vi /etc/pacman.d/mirrorlist
         
     更新本地数据库：
     
         # pacman -Syy

5.分区
   1)分区方案：
   
     $ lsblk /dev/sda -o NAME,FSTYPE,SIZE,MOUNTPOINT
       NAME   FSTYPE   SIZE MOUNTPOINT
       sda           120G 
       ├─sda1 ext4    36G /
       ├─sda2 ext4    80G /home
       └─sda3 swap     4G [SWAP]
       
   注意：swap分区大小建议等于或两倍于内存。
   2)分区：
      MBR 分区：
      
         # cfdisk /dev/sda
         
      GPT 分区：
      
         # cgdisk /dev/sda

6.创建文件系统：
   创建 ext4 分区：
   
         # mkfs.ext4 /dev/sda1
         # mkfs.ext4 /dev/sda2
         
   创建并激活 swap 分区：
   
         # mkswap /dev/sda3
         # swapon /dev/sda3
         
7.系统安装
   1)挂载分区：
       挂在 / 分区：
       
         # mount /dev/sda? /mnt
         
       挂在home分区：
       
         # mkdir /mnt/home
         # mount /dev/sda?  /mnt/home

   2)安装基础系统：
   
         # pacstrap -i /mnt base
         
   3)生成fstab
      
        # genfstab -U -p /mnt >> /mnt/etc/fstab
        
8.进入新系统

        # arch-chroot /mnt /bin/bash
        
9.配置local

    1）编辑/etc/locale.gen，移除需要的行前的 #：
    
        # vi /etc/locale.gen
        
      简体中文用户，可移除下列行前的 # ：
  
        #zh_CN.UTF-8 UTF-8 
        
    2)更新设置：
    
        # locale-gen
        
    3)设置默认语言
    
        # echo LANG=<语言>  > /etc/locale.conf
    
    4)简体中文用户可执行：

        # echo LANG=zh_CN.UTF-8  > /etc/locale.conf
        
    5)时区
    
    查看时区：
    时区：

          # ls /usr/share/zoneinfo/

    子时区：
    
          # ls /usr/share/zoneinfo/<时区>/

    设置时区：
    
          # ln -s /usr/share/zoneinfo/<时区>/<子时区> /etc/localtime
    
    中国大陆用户可执行：
    
          # ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
          
    6)硬件时间：
    
    推荐）使用UTC时间：
    
          # hwclock --systohc --utc

     使用本地时间：

      !__ 使用本地时间可能会引起某些不可修复的bug。

         # hwclock --systohc --localtime

10.创建 ramdisk 环境

（可选）配置

           # vi /etc/mkinitcpio.conf

  创建：

           # mkinitcpio -p linux

11.为 root 用户设置密码

           # passwd

12. 安装Grub引导器

      1)安装 BIOS + MBR：

           # pacman -S grub
           # grub-install --target=i386-pc --recheck /dev/sda
    
       2）生成配置文件：

            # grub-mkconfig -o /boot/grub/grub.cfg

       3）欲引导其他系统，如 Windows ，可执行：

             # pacman -S os-prober
             # grub-mkconfig -o /boot/grub/grub.cfg


13. 退出安装环境

退回安装环境

              # exit
卸载新分区

              # umount -R /mnt

重启
              # reboot

14. 用户管理

     1）添加一个典型的桌面用户：

           #  useradd -m -g users -G wheel -s /bin/bash <用户名>

       2）设置密码：
 
            # passwd <用户名>

       4)配置 sudo:

         安装 sudo：

          # pacman -S sudo
 
        配置：
          # EDITOR=nano visudo

        反注释该行（删去 # ）：
          
          # %wheel ALL=(ALL) ALL

       5)将允许获取 root 权限的用户加入 wheel 用户组（若用户已在该组则跳过）

           # gpasswd -a <用户名> wheel

       6)使用 sudo 时，在输入 <命令> 部分 Tab 补全会失效。如需启用 sudo 的 Tab 补全，执行：

         $ sudo pacman -S  bash-completion
         $ echo complete -cf sudo >> ~/.bashrc


15.声音管理

        1)安装 alsa-utils 

                 # pacman -S alsa-utils

         2)配置：
 
                 # alsamixer


16.  配置 GUI（图形用户界面）

       1) 安装显卡驱动:

            确定显卡型号：

                      $ lspci | grep VGA
                   
           对应可选驱动包：
    
        Intel 显卡：xf86-video-intel
        nVidia 显卡：
        GeForce 7 以上：xf86-video-nouveau；nvidia
        GeForce 6/7：xf86-video-nouveau；nvidia-304xx
        AMD/ATI 显卡：xf86-video-ati；AMD Catalyst（Arch Wiki）
       
     安装驱动：

                # pacman -S <驱动包>


       2)  安装 X 窗口系统:

         X 窗口系统（X11/X）是诸多桌面环境的基础。

         安装 Xorg Server

                     # pacman -S xorg-server xorg-server-utils xorg-xinit

         添加3D支持

                   # pacman -S mesa
         
          添加触摸板支持

                    # pacman -S xf86-input-synaptics
          安装字体

        （推荐）Dejavu和文泉驿-微米黑：

                    # pacman -S ttf-dejavu wqy-microhei

17. 安装桌面环境

Arch Linux 官方提供支持的桌面环境：
Cinnamon（官网）：cinnamon
Enlightenment（官网）：enlightenment17
GNOME（官网)：gnome、gnome-extra（附加功能包）
KDE（官网)：kde（完全版）、kdebase（基础版）、kde-l10n-zh_cn（简体中文包）
LXDE（官网)：lxde。
Xfce（官网)：xfce4、xfce4-goodies（附加功能包）
其他桌面环境：Arch Wiki
更加轻量级的窗口管理器：：Arch Wiki

安装：

                 # pacman -S <桌面环境组>

18. 启动桌面环境

启动方式有两种：从命令行以 startx 启动；使用显示管理器（Display Manager）。

startx：（以GNOME为例）

配置：

           $ cp /etc/skel/.xinitrc ~
           $ vi ~/.xinitrc

反注释对应行：

           # exec gnome-session
启动：

           $ startx

显示管理器：（以GDM为例）
从命令行启动：

           # systemctl start gdm.service

随系统启动：

           # systemctl enable gdm.service

19. 安装输入法

 fcitx：

安装：

         # pacman -S fcitx-im

配置：

         export GTK_IM_MODULE=fcitx
         export QT_IM_MODULE=fcitx
         export XMODIFIERS="@im=fcitx"

KDM、GDM、LightDM 等显示管理器的用户，向 ~/.xprofile添加以上内容。
使用 startx 或 slim 的用户，向 ~/.xinitrc 添加以上内容。
为在GNOME上使用fcitx，需禁用iBus：

         $ gsettings set org.gnome.settings-daemon.plugins.keyboard active false

20. AUR（Arch 用户软件仓库）

Arch Linux预设几个软件仓库中可能没有用户需要的软件包。遇到这种情况时，
可上AUR检索。AUR是是为用户而建、由用户主导的Arch软件仓库。AUR中的
软件包以软件包生成脚本（PKGBUILD）的形式提供，用户自己通过makepkg
生成包，再由pacman安装。创建AUR的初衷是方便用户维护和分享新软件包，
并由官方定期从中挑选软件包进入[community]仓库。
Yaourt是由社区编写的脚本，方便用户从AUR上获取需要的软件。以下介绍
Yaourt的安装和使用。

1) 安装

Yaourt并不在官方软件源中，除从AUR中安装，还可通过第三方软件源进行安装：

添加软件源：

将下列内容添加至 /etc/pacman.conf ：

源1：
 
               [archlinuxfr]
               SigLevel = Never
               Server = http://repo.archlinux.fr/$arch

源2（Arch Linux CN 友情提供 :) ）：

             [archlinuxcn]
             SigLevel = Never
             Server = http://repo.archlinuxcn.org/$arch

安装：

             # pacman -Sy base-devel yaourt
2) 使用

Yaourt 可以安装来自官方源的软件：

             $ yaourt <软件名>

更新所有包（包括来自官方源的包）：

             $ yaourt -Syua
搜索：

$ yaourt <关键字>
