---
layout: post
title: 记RaspberryPi3安装及配置
categories: [技术] 
description: 记RaspberryPi3安装及配置
keywords: linux
---


树莓派挂了。 网口灯不亮了，绿指示灯也不闪了。内存卡插到电脑上发现读不出来了。幸好插上还能有反应 那就重新格式化重头再来。 

# 一.烧系统

1. 格式化sd卡。  推荐使用sd formatter 工具：[https://www.sdcard.org/downloads/formatter\_4/eula\_windows/index.html](https://www.sdcard.org/downloads/formatter_4/eula_windows/index.html)。 最好别用windows系统自带的格式化。 文件格式是fat 和ntfs 好像不符合要求. 
2. 在官网下载系统 [https://www.raspberrypi.org/downloads/](https://www.raspberrypi.org/downloads/)   根据需要选择下载 我下载的是2017.08 发布的Raspbian Stretch Lite . 下载zip包解压
3. 下载Win32 Disk Imager  https://sourceforge.net/projects/win32diskimager/ 。选择解压后的系统，格式化好的sd卡。将系统写入。 *

# 二.登录树莓派
1. 开启ssh登录   后来的系统默认关闭了ssh登录。有显示器可以直接进入开启。  或者： 打开win10的powershell，进入sd卡所在盘，在根目录新建一个名为ssh的空白文件 输入： new-item ssh -type file 。 
2. 开机获取ip   将烧好的sd卡插入树莓派，接好网线。  正常会看到电源灯红灯常亮，旁边的绿灯闪烁。 网口灯也闪烁。 获取树莓派的ip有两种方法：

  a. 下载局域网ip扫描工具advanced ip scanner：[http://www.advanced-ip-scanner.com/cn/ ](http://www.advanced-ip-scanner.com/cn/) 
  b. 在powershell下用arp -a 扫描ip 。 记下开启树莓派前后变化的ip地址就是。 (好像大多树莓派设备的mac地址都是以b8-打头的 
  
  3. ssh登录 使用putty：[https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) 进行 ssh登录 。默认账号是pi 密码是 raspberry 
  
# 三.基础环境配置

1. 更新安装源,安装vim

```bash
sudo apt-get update;
sudo apt-get install vim;
```

2. 配置静态ip
  新系统不在之前的interfaces内配置了。 a.首先 ifconfig 查看网卡配置，记下网卡的名字，我这里是 enxb827eb022075   b.   sudo vim /etc/dhcpcd.conf 将# Example static IP configuration:下面几行的注释去掉  根据自身情况修改对应的值:

```bash
\# Example static IP configuration:
interface enxb827eb022075  #这是之前查看的网卡的名字
static ip_address=172.25.1.105/24   #这是静态ip地址及子网掩码
static routers=172.25.1.254    #默认路由
static domain\_name\_servers=210.42.35.35 8.8.8.8  #DNS服务器

```

3. 最后sudo reboot 使用修改后的ip地址登录 有问题参考：[https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address/37921#37921](https://raspberrypi.stackexchange.com/questions/37920/how-do-i-set-up-networking-wifi-static-ip-address/37921#37921) 3.**安装shadowsock-libev：**  [https://github.com/shadowsocks/shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)


# 四：U盘装系统， sd卡引导
  内存卡果然出了问题，一不小心就崩。。。 照前面的步骤先把 系统写在u盘上，修改boot内的 cmdline.txt 文件中root=XXXX部分为  root=/dev/sda2  (u盘分为了两个区，sda1为boot分区，sda2为系统内核所在区)， 然后将u盘boot分区内所有文件复制到sd卡内，插好sd卡和u盘。  开机启动。 这时sd卡起到一个开机引导作用，加载u盘内的系统。 树莓派3也支持完全脱离sd卡，直接从u盘启动

