

# 安装CentOS



- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [CentOS Minimal](http://101.110.118.58/isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1611.iso)

### 安装CentOS

下载好CentOS7 Minimal([下载链接](https://www.centos.org/download/))的ISO镜像之后，就开始系统的安装(我的VirtualBox是英文版），步骤如下：

1. 打开VirtualBox点击左上角的 New新建，填入Name、Tyepe(Linux)、Version(==Red Hat(64-bit==)),Continue
2. 设置Memory size，这里我设置为1024M就够了，Continue
3. 设置Hard disk，默认(Create a virtual hard disk now)，Create
4. 设置Hard disk file type，默认(VDI)，Continue
5. 设置Storage on physical hard disk，默认(Dynamically allocated)，Continue
6. 设置File location and size， 默认位置，磁盘大小设为8G，Create

设置完成点击Start启动虚拟机，这时候弹框需要输入一个系统镜像，选择之前下载完成的CentOS镜像文件路径，点Start开始安装。安装过程很简单，系统安装位置选默认分区点一下，进行下一步进行安装，安装时间会比较长，过程中设置root密码，是否创建新用户由自己决定。

系统安装好之后点击重启，进入系统出现黑框要求输入登录账号和密码，系统就装好了。

### 设置网络

系统安装好之后启动，一般不能直接访问网络，这是因为网卡没有启动。这里需要手动做一下配置。

```
[root@localhost ~]# curl www.zkt.name
curl: (6) Could not resolve host: www.zkt.name; Unknown error  

```

输入命令`ip addr`可以看到有两个网卡`lo、enp0s3`前者的地址是`127.0.0.1`，后者没有ip地址，这里需要手动配置网卡enp0s3自动启动，打开vi /etc/sysconfig/network-scripts/编辑文件名为ifcfg-enp0s3内容设置`ONBOOT=yes`，然后重启系统`reboot`，IP获取正常，可以访问网络了。





# 