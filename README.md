# 小米路由器AX3000T解锁SSH，安装ShellCrash直接科学上网，ShellClash
小米路由器AX3000T解锁SSH，安装ShellCrash直接科学上网，不用刷机openwrt<br>
视频教程：▶ https://youtu.be/NfQ-ELR-TD8

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
1.用MobaXterm将uboot文件上传到路由器的 /tmp 文件夹

2.输入 cd /tmp 命令进入 /tmp 目录。

3.输入 mtd write mt7981_xiaomi_ax3000t-u-boot.bin FIP 命令以进行刷机操作。

4.路由器断电后，用针按住 reset 不放，再接上电源，等待 10s 左右松开，电脑用网线和路由器的网口1连接，电脑在网络设置里将以太网ipv4设置为静态。IP地址： 192.168.1.5 ，子网掩码： 255.255.255.0 ，浏览器打开 192.168.1.1 访问uboot后台。

5.进入uboot后台刷入固件，刷openwrt的话分区就选 qwrt ,刷squashfs-sysupgrade格式的就可以，不行就先刷initramfs-kernel然后再到后台去升级为squashfs-sysupgrade格式的固件，如果失败或设备进不了系统就再刷一次，这个可能要多试几次,我也刷了几次才成功。

Thanks to all the original authors!
