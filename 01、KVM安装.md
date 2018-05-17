# KVM安装
前提：1、在服务器上安装centos7.4；2、安装epel源

##安装KVM  
```bash
yum -y install libcanberra-gtk2 qemu-kvm.x86_64 qemu-kvm-tools.x86_64  libvirt.x86_64 libvirt-cim.x86_64 libvirt-client.x86_64
yum -y install libvirt-java.noarch  libvirt-python.x86_64 libiscsi-1.7.0-5.el6.x86_64  dbus-devel  virt-clone tunctl virt-manager libvirt libvirt-python python-virtinst virt-viewer
yum install -y dejavu-lgc-sans-fonts
yum groupinstall "Fonts" --setopt=group_package_types=mandatory,default,optional
yum groupinstall "X Window System" --setopt=group_package_types=mandatory,default,optional
systemctl start libvirtd
systemctl enable libvirtd
```

备注：centos7.4在安装的时候建议安装server with gui，因为创建kvm标准镜像的时候需要规划虚拟机的磁盘分区，需要使用virt-manager来图形化安装kvm镜像。

##安装vncserver
```bash
yum -y install tigervnc-server tigervnc
cp /lib/systemd/system/vncserver@.service /lib/systemd/system/vncserver@:1.service
修改vnc的service文件为如下内容
more /lib/systemd/system/vncserver@:1.service
# The vncserver service unit file
#
# Quick HowTo:
# 1. Copy this file to /etc/systemd/system/vncserver@.service
# 2. Replace <USER> with the actual user name and edit vncserver
#    parameters appropriately
#   ("User=<USER>" and "/home/<USER>/.vnc/%H%i.pid")
# 3. Run `systemctl daemon-reload`
# 4. Run `systemctl enable vncserver@:<display>.service`
#
# DO NOT RUN THIS SERVICE if your local area network is
# untrusted!  For a secure way of using VNC, you should
# limit connections to the local host and then tunnel from
# the machine you want to view VNC on (host A) to the machine
# whose VNC output you want to view (host B)
#
# [user@hostA ~]$ ssh -v -C -L 590N:localhost:590M hostB
#
# this will open a connection on port 590N of your hostA to hostB's port 590M
# (in fact, it ssh-connects to hostB and then connects to localhost (on hostB).
# See the ssh man page for details on port forwarding)
#
# You can then point a VNC client on hostA at vncdisplay N of localhost and with
# the help of ssh, you end up seeing what hostB makes available on port 590M
#
# Use "-nolisten tcp" to prevent X connections to your VNC server via TCP.
#
# Use "-localhost" to prevent remote VNC clients connecting except when
# doing so through a secure tunnel.  See the "-via" option in the
# `man vncviewer' manual page.


[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
User=root

# Clean any existing files in /tmp/.X11-unix environment
#ExecStartPre=-/usr/bin/vncserver -kill %i
#ExecStart=/usr/bin/vncserver %i
#PIDFile=/home/<USER>/.vnc/%H%i.pid
#ExecStop=-/usr/bin/vncserver -kill %i
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill :1 > /dev/null 2>&1 || :'
ExecStart=/sbin/runuser -l root -c "/usr/bin/vncserver :1 -geometry 1280x720 -depth 24"
PIDFile=/root/.vnc/%H%i.pid 
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill :1 > /dev/null 2>&1 || :'
[Install]
WantedBy=multi-user.target
启动vncserver
systemctl restart vncserver@.service
```
安装vnc之后可以在笔记本或者台式机上安装vnc viewer来直接远程桌面连接到linux服务器。

