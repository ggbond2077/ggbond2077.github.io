1. 硬件 
2. iso 安装pve
常规配置 https://dj9399.github.io/post/proxmox(pve)%20LEDE%20OMV%E7%AC%94%E8%AE%B0/
3. local local-lvm ? 大小 30g 60g
	删除重建local-lvm
	https://forum.proxmox.com/threads/reduce-size-of-local-lvm.78676/
	扩大local https://www.reddit.com/r/Proxmox/comments/ctbtic/resizing_local_and_locallvm/
4. 安装nas openmediavault https://www.chiphell.com/forum.php?mod=viewthread&tid=1917683&page=1
https://www.rosnas.com/1178.html
硬盘格式化 ext4, https://wangxingcs.com/2020/0910/1442/  直通omv https://www.itheader.com/784.html
https://pve.proxmox.com/wiki/Passthrough_Physical_Disk_to_Virtual_Machine_(VM)

5. 开外网访问 & 防火墙
https://www.jianshu.com/p/586da7c8fd42
https://www.quyu.net/info/1589.html

6. omv 挂载磁盘 设置共享文件夹 http://loonlog.com/2020/1/9/openmediavault-Create-Shared-directory/

7. 插件:
https://github.com/OpenMediaVault-Plugin-Developers/installScript

页面上点击安装docker  portainer
安装 qbitttorrent https://zhuanlan.zhihu.com/p/363378341
类似方法安装plex

qbittorrent web ui

m-team pt 联动
浏览器插件: https://github.com/ronggang/PT-Plugin-Plus

qbittorrent plex 联动
https://sleele.com/2020/03/16/%E9%AB%98%E9%98%B6%E6%95%99%E7%A8%8B-%E8%BF%BD%E5%89%A7%E5%85%A8%E6%B5%81%E7%A8%8B%E8%87%AA%E5%8A%A8%E5%8C%96/

plex 外网访问


samba服务 windows ios
挂 pt 

3. 用户权限
安全

keepass 个人密码保护

本地域名访问
外网域名访问
阿里云ddns

ups保护,  远程开关机
pve备份
omv备份

安装openwrt 开vpn代理
https://www.10bests.com/install-openwrt-lede-on-pve/

wireguard 代理
https://devld.me/2020/07/27/wireguard-setup/
https://www.v2ex.com/t/736163
