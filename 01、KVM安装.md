# KVM安装
前提：1、在服务器上安装centos7.4；2、安装epel源

开始安装KVM  
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
