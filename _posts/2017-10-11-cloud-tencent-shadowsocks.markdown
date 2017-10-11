---
layout: post
title:  "腾讯云+shadowsocks小记"
tags:
  - shadowsocks
  - 腾讯云
  - 云服务器
  - ubuntu
---

基于腾讯云搭建一个shadowsocks，还算比较简单的，遇到几个小坑，过程有惊无险，特记录一下。

## 购买腾讯云服务器
官网：`https://cloud.tencent.com/`
过程不表，记得不要选国内区域即可。
我买的香港一区，1核 1GB 2Mbps，系统Ubuntu


## 一些配置
> 挂载硬盘及启用root
> 与新服务器有关，与shadowsocks无关

因为买的时候还买了一块数据盘，但是腾讯云不会自动挂载，因此需要挂载到系统，否则浪费了
登录：`ssh ubuntu@*.*.*.*`
检查一下看不到数据盘
``` bash
ubuntu@ubuntu:/$ df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              423264       0    423264   0% /dev
tmpfs              88508    2796     85712   4% /run
/dev/vda1       51474044 1694048  47158612   4% /
tmpfs             442540      24    442516   1% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs             442540       0    442540   0% /sys/fs/cgroup
tmpfs              88508       0     88508   0% /run/user/500

```

换个命令查看硬盘信息
``` bash
ubuntu@ubuntu:~$ sudo fdisk -l
Disk /dev/vda: 50 GiB, 53687091200 bytes, 104857600 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x27921508

Device     Boot Start       End   Sectors Size Id Type
/dev/vda1  *     2048 104857599 104855552  50G 83 Linux


Disk /dev/vdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

```

格式化硬盘（数据盘）
``` bash
ubuntu@ubuntu:/$ sudo mkfs.ext4 /dev/vdb
mke2fs 1.42.13 (17-May-2015)
Creating filesystem with 5242880 4k blocks and 1310720 inodes
Filesystem UUID: 1398974e-576b-4168-ab80-5e49c7bd374d
Superblock backups stored on blocks: 
  32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
  4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 
```
设置挂载点及开机挂载
``` bash
ubuntu@ubuntu:/etc$ echo '/dev/vdb /data ext4 defaults 0 0' >> /etc/fstab
-bash: /etc/fstab: Permission denied
ubuntu@ubuntu:/etc$ sudo echo '/dev/vdb /data ext4 defaults 0 0' >> /etc/fstab
-bash: /etc/fstab: Permission denied

```
看来出错了，权限不够，sudo也不行
切换root用户试试
``` bash
ubuntu@ubuntu:~$ su root
Password: 
su: Authentication failure
```
应该是没有设置root密码，不能切换
那就先设密码
``` bash
ubuntu@ubuntu:~$ sudo passwd root
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```
设好后`su root`切换用户，接着执行`echo '/dev/vdb /data ext4 defaults 0 0' >> /etc/fstab`
然后`mount -a`，挂载全部的意思？

这个时候可以看到磁盘信息了
``` bash
root@ubuntu:~# df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              423264       0    423264   0% /dev
tmpfs              88508    3908     84600   5% /run
/dev/vda1       51474044 1694880  47157780   4% /
tmpfs             442540      24    442516   1% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs             442540       0    442540   0% /sys/fs/cgroup
tmpfs                100       0       100   0% /run/lxcfs/controllers
tmpfs              88508       0     88508   0% /run/user/500
/dev/vdb        20511356   44992  19401404   1% /data
```

保险起见，我重启了服务器`reboot`



## 安装shadowsocks

参照[官方指引]: https://github.com/shadowsocks/shadowsocks/tree/master
因为是新服务器，pip都还没有安装
``` bash
ubuntu@ubuntu:~$ su root
Password: 
root@ubuntu:/home/ubuntu# pip
The program 'pip' is currently not installed. You can install it by typing:
apt install python-pip
root@ubuntu:/home/ubuntu# apt-get install python-pip
```

pip安装好了，可以安装shadowsocks了
``` bash
root@ubuntu:/home/ubuntu# pip install git+https://github.com/shadowsocks/shadowsocks.git@master
Traceback (most recent call last):
  File "/usr/bin/pip", line 11, in <module>
    sys.exit(main())
  File "/usr/lib/python2.7/dist-packages/pip/__init__.py", line 215, in main
    locale.setlocale(locale.LC_ALL, '')
  File "/usr/lib/python2.7/locale.py", line 581, in setlocale
    return _setlocale(category, locale)
locale.Error: unsupported locale setting

```
出错了，网上查了资料，具体原因我也不清楚，
好在解决方法很简单，执行`export LC_ALL=C`，然后再安装shadowsocks
``` bash
root@ubuntu:~# export LC_ALL=C
root@ubuntu:~# pip install git+https://github.com/shadowsocks/shadowsocks.git@master
Collecting git+https://github.com/shadowsocks/shadowsocks.git@master
  Cloning https://github.com/shadowsocks/shadowsocks.git (to master) to /tmp/pip-_ucYLA-build
Installing collected packages: shadowsocks
  Running setup.py install for shadowsocks ... done
Successfully installed shadowsocks-3.0.0

```
安装完成！！


## 配置shadowsocks

可以先命令行启动测试一下`ssserver -p 443 -k password -m aes-256-cfb`
然后发现连接不到，这个时候想到可能是端口问题，
于是回到腾讯云服务器管理后台，配置安全组
之前是只放通22端口，先改成放通全部端口，这样可能不太安全，不过先配置shadowsocks好再说。
大约等几分钟，客户端可以连接访问了。

但是这只是调通了，参数都是写在命令中，且关掉命令窗口就会关掉服务。
于是继续按照[官方指引]: https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File
设置配置文件启动

首先建立配置文件`vi shadowsocks.json`，输入内容如下
``` json
{
  "server":"*.*.*.*",
  "server_port":8388,
  "local_address":"127.0.0.1",
  "local_port":1080,
  "password":"password",
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open":false
}

```
尝试启动
``` bash
root@ubuntu:/etc# ssserver -c /etc/shadowsocks.json 
INFO: loading config from /etc/shadowsocks.json
2017-10-11 15:22:39 INFO     loading libcrypto from libcrypto.so.1.0.0
2017-10-11 15:22:39 INFO     starting server at *.*.*.*:8388
Traceback (most recent call last):
  File "/usr/local/bin/ssserver", line 9, in <module>
    load_entry_point('shadowsocks==3.0.0', 'console_scripts', 'ssserver')()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/server.py", line 74, in main
    tcp_servers.append(tcprelay.TCPRelay(a_config, dns_resolver, False))
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/tcprelay.py", line 754, in __init__
    server_socket.bind(sa)
  File "/usr/lib/python2.7/socket.py", line 228, in meth
    return getattr(self._sock,name)(*args)
socket.error: [Errno 99] Cannot assign requested address
```
出错了，网上查资料，修改配置文件的`server`如下：
``` json
{
  "server":"0.0.0.0",
  "server_port":8388,
  "local_address":"127.0.0.1",
  "local_port":1080,
  "password":"password",
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open":false
}

```
终于可以了
``` bash
root@ubuntu:/etc# ssserver -c /etc/shadowsocks.json 
INFO: loading config from /etc/shadowsocks.json
2017-10-11 15:34:37 INFO     loading libcrypto from libcrypto.so.1.0.0
2017-10-11 15:34:37 INFO     starting server at 0.0.0.0:8388
```


多用户配置，参考: https://github.com/shadowsocks/shadowsocks/wiki/Configure-Multiple-Users

```
root@ubuntu:/etc# cat shadowsocks.json
{
  "server":"0.0.0.0",
  "port_password": {
    "8381":"p1",
    "8382":"p2",
    "8383":"p3",
    "8384":"p4"
  },
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open":false,
  "workers":3
}

```

后台启动
```
root@ubuntu:~# ssserver -c /etc/shadowsocks.json -d start
INFO: loading config from /etc/shadowsocks.json
2017-10-11 15:43:52 INFO     loading libcrypto from libcrypto.so.1.0.0

```
大功告成！！











