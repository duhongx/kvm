# KVM快速创建虚拟机

1、KVM快速创建虚拟机是根据镜像直接克隆出来的，方式如下：
```bash
创建名称为k8s-1的虚拟机
[root@k8s-test-1 ~]# virt-clone --connect qemu:///system --original-xml /var/lib/libvirt/images/template.xml --name k8s-1 --file /var/lib/libvirt/images/k8s-1.qcow2
Allocating 'k8s-1.qcow2'                                                                                                                                                                                                                               | 100 GB  00:00:02     

Clone 'k8s-1' created successfully.
[root@k8s-test-1 ~]# virsh list --all
 Id    Name                           State
----------------------------------------------------
 -     192-168-2-111                  shut off
 -     k8s-1                          shut off

大概5-10秒即可创建成功，创建完成的虚拟机是关机状态的
```

2、修改虚拟机的ip地址，需要安装镜像文件的操作工具，然后先把网卡的配置文件从虚拟机磁盘镜像中拷贝出来，放到本地目录下，修改完成后再拷贝回去到虚拟机的磁盘镜像里，操作方式如下：  
```bash
[root@k8s-test-1 ~]# yum -y install libguestfs-tools
[root@k8s-test-1 ~]# virt-copy-out -d k8s-1 /etc/sysconfig/network-scripts/ifcfg-eth0 /tmp/
[root@k8s-test-1 ~]# more /tmp/ifcfg-eth0 
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=7f5d24f9-edb3-45e8-a2e0-b3e8cc331052
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.2.112
NETMASK=255.255.255.0
GATEWAY=192.168.2.1
[root@k8s-test-1 ~]# sed -i 's/192.168.131.112/192.168.2.112/g' /tmp/ifcfg-eth0 
[root@k8s-test-1 ~]# sed -i 's/192.168.2.1/192.168.2.1/g' /tmp/ifcfg-eth0 
[root@k8s-test-1 ~]# virt-copy-in -d k8s-1 /tmp/ifcfg-eth0 /etc/sysconfig/network-scripts/
因为我这里不需要修改第一台虚拟机的地址，因此替换前后ip未变化，后续就需要根据实际的ip地址进行替换。
```

3、启动虚拟机并登陆  
```bash
[root@k8s-test-1 ~]# virsh start k8s-1
Domain k8s-1 started

[root@k8s-test-1 ~]# virsh list --all
 Id    Name                           State
----------------------------------------------------
 4     k8s-1                          running
 -     192-168-2-111                  shut off

[root@k8s-test-1 ~]# ssh 192.168.2.112
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:syIV6feG7qty5g2KhOrIArdEJl9n4PrBAQE5OzywcM4.
Please contact your system administrator.
Add correct host key in /root/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /root/.ssh/known_hosts:2
ECDSA host key for 192.168.2.112 has changed and you have requested strict checking.
Host key verification failed.
[root@k8s-test-1 ~]# >/root/.ssh/known_hosts 
[root@k8s-test-1 ~]# ssh 192.168.2.112
The authenticity of host '192.168.2.112 (192.168.2.112)' can't be established.
ECDSA key fingerprint is SHA256:syIV6feG7qty5g2KhOrIArdEJl9n4PrBAQE5OzywcM4.
ECDSA key fingerprint is MD5:d6:9f:6b:07:65:a4:12:07:41:9c:00:04:e5:a3:80:85.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.2.112' (ECDSA) to the list of known hosts.
root@192.168.2.112's password: 
[root@localhost ~]# 
```
