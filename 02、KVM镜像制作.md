#KVM镜像制作

##KVM安装centos7.4虚拟机   
方法1：使用virt-manager来安装一个虚拟机，时区设置为Asia/Shanghai，最小的程序安装，然后自定义分区，根据实际使用情况进行分区，本地的分区选择标准分区，未采用LVM分区，swap 4096M，剩余的全部磁盘空间都是/分区。磁盘是qcow2格式的，大小为100G。如果磁盘不够后续再添加磁盘。  

方法2：使用virt-install来安装虚拟机，virt-install --name default --ram 1024 --vcpus=2 --location=/home/CentOS-7-x86_64-DVD-1708.iso --disk path=/var/lib/libvirt/images/default.qcow2,size=100,format=qcow2 --network bridge=br0 --os-type=linux --os-variant=rhel7 --nographics --extra-args='console=ttyS0' --force这种安装为文本方式安装，就算分区选择自定义，实际上centos7的默认分区为/boot swap /分区50G，其他的全部为/home分区，这种分区不太合理，现在很多程序修改默认的数据存储位置都很麻烦。  
强烈推荐采用第一种安装，自定义分区；


