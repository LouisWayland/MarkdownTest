# [Ubuntu1804 系统](../README.md) #
## 准备工作(PC机环境Ubuntu1604) ##
`sudo apt-get install qemu-user-static`

`sudo apt-get install repo git-core gitk git-gui gcc-arm-linux-gnueabihf u-boot-tools device-tree-compiler`

`sudo apt-get install gcc-aarch64-linux-gnu mtools parted libudev-dev libusb-1.0-0-dev python-linaro-image-tools`

`sudo apt-get install linaro-image-tools gcc-4.8-multilib-arm-linux-gnueabihf gcc-arm-linux-gnueabihf libssl-dev`

`sudo apt-get install gcc-aarch64-linux-gnu g+conf autotools-dev libsigsegv2 m4 intltool libdrm-dev curl sed make`

`sudo apt-get install binutils build-essential gcc g++ bash patch gzip bzip2 perl tar cpio python unzip rsync file bc wget` 
 
`sudo apt-get installlibncurses5 libqt4-dev libglib2.0-dev libgtk2.0-dev libglade2-dev cvs git mercurial rsync openssh-client`  

`sudo apt-get install subversion asciidoc w3m dblatex graphviz python-matplotlib libc6:i386 libssl-dev texinfo`  

`sudo apt-get installliblz4-tool genext2fs lib32stdc++6`  

## firefly官方根文件系统（lubuntu）##

### 根文件系统基础配置 ###
拷贝AIO-3399C-UBUNTU18.04-GPT-20190304-1225.img镜像到虚拟机内  
`mkdir rootfs`  
`sudo mount AIO-3399C-UBUNTU18.04-GPT-20190304-1225.img rootfs`  
`mkdir teworootfs`  
`sudo cp -rfp rootfs/* teworootfs`  
`sudo umount rootfs`    
`sudo cp -b /etc/resolv.conf teworootfs/etc/resolv.conf`  
`sudo cp /usr/bin/qemu-aarch64-static teworootfs/usr/bin/`  


### 用户配置 ###
进入qemu模拟器  
`sudo chroot teworootfs`  
`apt update`  
`apt upgrade`  

`useradd -s '/bin/bash' -m -G adm,sudo tewo`  
`userdel firefly`  
`passwd tewo`  
`passwd root`  
`adduser tewo sudo`  
`adduser tewo dialout`
`adduser tewo input`

开机免密码登录  
`vim /etc/lightdm/lightdm.conf.d/110-lubuntu.conf`
添加如下内容：

    [SeatDefaults]
    autologin-user=tewo  
    
    xserver-command=X -bs -core -nocursor #隐藏鼠标  

ttysWK串口修改所属组  
`vim /etc/udev/rules.d/110-com.rules`    
添加如下内容  
`KERNEL=="ttysWK[0-9]*", NAME="ttysWK/%n", SYMLINK+="%k", GROUP="dialout"`  

### 应用安装 ###
强制解析mesa-common-dev、libgles2、libegl1、libglvnd-dev  
`dpkg -i --force-overwrite /var/cache/apt/archives/mesa-common-dev_19.0.2-1ubuntu1.1~18.04.1_arm64.deb`   
`dpkg -i --force-overwrite /var/cache/apt/archives/libgles2_1.0.0-2ubuntu2.3_arm64.deb`   
`dpkg -i --force-overwrite /var/cache/apt/archives/libegl1_1.0.0-2ubuntu2.3_arm64.deb`    
`dpkg -i --force-overwrite /var/cache/apt/archives/libglvnd-dev_1.0.0-2ubuntu2.3_arm64.deb`   
`apt --fix-broken install`   
qt5运行环境  
`apt install libqt5*`  
qml运行环境  
`apt install qml-module*`  
ssh远程环境
`apt install openssh-server`
gdb调试环境   
`apt install gdbsercer`

## Ubuntu-base ##
### 根文件系统基础配置 ###
[ubuntu cdimg](http://cdimage.ubuntu.com/ubuntu-base/releases/18.04/release/)下载基础根文件系统  
选择	ubuntu-base-18.04-base-arm64.tar.gz  
`mkdir tewofs`  
`sudo tar zxpf ubuntu-base-18.04-base-arm64.tar.gz -C tewofs` 
`sudo cp -b /etc/resolv.conf tewofs/etc/resolv.conf`  
`sudo cp /usr/bin/qemu-aarch64-static tewofs/usr/bin/`  


### 用户配置 ###
进入qemu模拟器   
`sudo chroot tewofs`  
`apt update`   
`apt upgrade`  

`useradd -s '/bin/bash' -m -G adm,sudo tewo`  
`passwd tewo`  
`passwd root`  
`adduser tewo sudo`  
`adduser tewo dialout`
`adduser tewo input`  
`apt install locales* sudo vim`
`chown -R tewo /opt/`

修改主机信息  
`vim /etc/hosts`  
添加如下内容：  

    127.0.0.1   localhost
    127.0.1.1   TewoGateway
    
    # The following lines are desirable for IPv6 capable hosts
    ::1 ip6-localhost ip6-loopback
    fe00::0 ip6-localnet
    ff00::0 ip6-mcastprefix
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters

`echo "TewoGateway" > /etc/hostname`  

DNS解析配置  
`vim /etc/systemd/resolved.conf`  

    [Resolve]
    DNS=8.8.8.8
    #FallbackDNS=
    #Domains=
    LLMNR=no
    #MulticastDNS=no
    #DNSSEC=no
    #Cache=yes
    #DNSStubListener=yes

NetworkManager配置  
`vim /etc/NetworkManager/NetworkManager.conf`  
    
    [main]
    plugins=ifupdown,keyfile,ofono
    dns=dnsmasq
    
    [ifupdown]
    managed=true
    
    [device]
    wifi.scan-rand-mac-address=no
	wifi.cloned-mac-address=

`echo "bind-interfaces" > /etc/dnsmasq.d/network-manager`  

`touch /etc/NetworkManager/conf.d/10-globally-managed-devices.conf`  

`chown -R tewo /opt/`  

### 应用安装 ###
  
`apt install network-manager network-manager-gnome iputils-ping`
  
`apt install net-tools ifupdown dnsmasq ofono`  

`apt install openssh-server openssh-client`  

`apt install mosquitto mosquitto-clients`  

`apt install xubuntu-core`

`apt install libqt5* qml-modules*`  

## 制作根文件系统镜像 ##
`dd if=/dev/zero of=rootfs.img bs=1M count=2048`  
**count 的值根据根文件系统的大小决定，实际值为bs\*count**  

`sudo mkfs.ext4 rootfs.img`

`sudo mount rootfs.img rootfs/`  

`sudo cp -rfp tewofs/*  rootfs/`  

`sudo umount rootfs/`  

`e2fsck -p -f rootfs.img`  

`resize2fs  -M rootfs.img`  

## 系统启动后配置 ##
查看根文件系统是否占满分区  
`df -h`  
扩展文件系统  
`sudo resize2fs 对应分区`








