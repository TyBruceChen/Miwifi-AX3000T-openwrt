# 小米路由器AX3000T解锁SSH，安装ShellCrash直接科学上网，ShellClash, OpenWrt

## 一、Xiaomi 路由器 AX3000T 解锁SSH:
### 1、Windows搜索cmd，以管理员身份运行 (for macOS, quotes ```"``` are required to apply to two sides at the link for the omission of considering ```;```.)：
```
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20set%20ssh_en%3D1%0A"
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/misystem/arn_switch -d "open=1&model=1&level=%0Anvram%20commit%0A"
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/misystem/arn_switch -d "open=1&model=1&level=%0Ased%20-i%20's%2Fchannel%3D.*%2Fchannel%3D%22debug%22%2Fg'%20%2Fetc%2Finit.d%2Fdropbear%0A"
curl -X POST http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/misystem/arn_switch -d "open=1&model=1&level=%0A%2Fetc%2Finit.d%2Fdropbear%20start%0A"
```
Optional: <br>
To disable the password at login: ```curl -X POST "http://192.168.31.1/cgi-bin/luci/;stok=XXXXXX/api/misystem/arn_switch" -d "open=1&model=1&level=%0Apasswd+-d+root+password%0A"```
   
## 2、计算SSH密码
https://miwifi.dev/ssh 输入路由器的SN码，在路由器后台可以查询SN码。

用putty软件登录路由器，<a href="https://github.com/eujc/AX3000T/releases/download/gongju/AX3000T.zip" target="_blank">点击下载>></a>

用户名是：root，密码是上一步计算出来的，提示 Are U OK 表示已经登录成功。

## 3、永久开启SSH（重启不会关闭）

    mkdir /data/auto_ssh && cd /data/auto_ssh
    curl -O https://fastly.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
    chmod +x auto_ssh.sh

    uci set firewall.auto_ssh=include
    uci set firewall.auto_ssh.type='script'
    uci set firewall.auto_ssh.path='/data/auto_ssh/auto_ssh.sh'
    uci set firewall.auto_ssh.enabled='1'
    uci commit firewall

## 二、安装ShellCrash（ShellClash）：
ShellCrash安装源：

    export url='https://fastly.jsdelivr.net/gh/juewuy/ShellCrash@master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

备用安装源：

    export url='https://gh.jwsc.eu.org/master' && sh -c "$(curl -kfsSl $url/install.sh)" && source /etc/profile &> /dev/null

<br>
Clash管理后台：http://192.168.31.1:9999/ui

## Or install OpenWRT from Uboot (from [zc360/Xiaomi-ax3000t-openwrt](https://github.com/zc360/Xiaomi-ax3000t-openwrt)):
1.用MobaXterm将uboot文件上传到路由器的 /tmp 文件夹t

2.输入 cd /tmp 命令进入 /tmp 目录。(or through scp transmission by: ```scp -O Downloads/mt7981_ax3000t-fip-fixed-parts-multi-layout.bin root@192.168.31.1:/tmp```, pay attention here we use ```-O``` to enforce our PC (mac) use the older scp protocol, rather than the new one which is sftp, since the sftp-server is not installed on the original busybox OS)

Optional & recommended: verify the firmware image package that is sent to the router: ```md5sum mt7981_ax3000t-fip-fixed-parts-multi-layout.bin```, which will return the verification code: ```d89f9ef5955840913428ee9eca69957f```. (from [Bilibili/OpenRouter](https://www.bilibili.com/video/BV1dj411h7EP/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=1db61c2ba3ce56a634310a7590956269))

3.输入 mtd write mt7981_xiaomi_ax3000t-u-boot.bin FIP 命令以进行刷机操作。

4.路由器断电后，用针按住 reset 不放，再接上电源，等待 10s 左右松开，电脑用网线和路由器的网口1连接，电脑在网络设置里将以太网ipv4设置为静态。IP地址： 192.168.1.5 ，子网掩码： 255.255.255.0 ，浏览器打开 192.168.1.1 访问uboot后台。

5.进入uboot后台刷入固件，刷openwrt的话分区就选 qwrt ,刷squashfs-sysupgrade格式的就可以，不行就先刷initramfs-kernel然后再到后台去升级为squashfs-sysupgrade格式的固件，如果失败或设备进不了系统就再刷一次，这个可能要多试几次,我也刷了几次才成功。

Extra Sources: <br>
[Xiaomi AX3000T info website (openwrt)](https://openwrt.org/inbox/toh/xiaomi/ax3000t) <br>
[MT7981B Doc (CPU from Cortex-A53 (Armv8-A architecture))](https://mirror2.openwrt.org/docs/MT7981B_Wi-Fi6_Platform_Datasheet_Open_V1.0.pdf) <br>
[Build customize Openwrt by P3TERX](https://github.com/P3TERX/Actions-OpenWrt)

## Debugging:
```
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:l4Vwh73pv2Ch4nsIkE+Ts66zySIY407q/fo7BsKuqOY.
Please contact your system administrator.
...know_host: 10
````
The warning message you're seeing indicates that the host key of the remote machine (192.168.31.1) has changed since the last time you connected to it. This can happen for a few reasons, such as the remote machine being reinstalled or its SSH configuration being changed. It could also indicate a man-in-the-middle attack, but if you know the change is legitimate, you can safely update your known_hosts file.

Remove the old host key from the known_hosts file:

    ssh-keygen -R 192.168.31.1
    
Or by manually removing the line 10 in ~/.ssh/know_host

# Thanks to all the original authors!
