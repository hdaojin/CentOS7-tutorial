# NFS

## 1. 基本概念

网络文件系统（英语：Network File System，缩写作 NFS）是一种分布式文件系统，力求客户端主机可以访问服务器端文件，并且其过程与访问本地存储时一样，它由Sun微系统（已被甲骨文公司收购）开发，于1984年发布。

它基于开放网络运算远程过程调用（ONC RPC）系统：一个开放、标准的RFC系统，任何人或组织都可以依据标准实现它。

### 1.1. 版本和产品

NFSv1 只在SUN公司内部用作实验目的。开发团队在NFSv1的基础上做了重大改进之后将其对外发布，版本NFSv2由此产生。

#### 1.1.1. NFSv2

NFSv2最初在SunOS 2.0上面实现，1985年发布。

参与NFSv2设计实现的人包括罗素·桑德柏格（Russel Sandberg）、鲍伯·里昂（Bob Lyon）、比尔·乔伊、史提夫·克莱曼（Steve Kleiman）等。NFSv2 的定义RFC 1094，于1989年3月发布。

NFSv2 最初只是基于 UDP。设计者旨在保持服务端是无状态的，而将“锁”等机制的实现独立于核心协议之外。 这是一个关键决定，它使从服务器故障恢复变得简单：当一个服务器变得不可用时，所有的网络客户端冻结，但一旦服务器恢复，每一个尝试重传的状态都包含在每个RPC里面，这是由客户端存根发起的。这样的设计决策允许UNIX应用程序可以忽视服务器端的问题。

虚拟文件系统接口很容易模块化地实现一个简单的协议。在1986年2月，诸多操作系统实现了对NFSv2的支持，例如 System V release 2、DOS，以及使用Eunice的VAX/VMS。

由于 32-bit 的限制，NFSv2 只允读写文件起始2G大小的内容。

#### 1.1.2. NFSv3

Version 3（RFC 1813，1995年6月）添加如下功能：

支持 64 bit 文件大小和偏移量，即突破 2GB 文件大小的限制；
支持服务端的异步写操作，提升写入性能；
在许多响应报文中额外增加文件属性，避免用到这些属性时重新获取；
增加 READDIRPLUS 调用，用于在遍历目录时获取文件描述符和文件属性；
其他改进。
在NFSv2发布后不久，NFSv3协议提案就在Sun Microsystems内部被提出，其主要目的是解决NFSv2进行同步写操作的性能问题。1992年7月的实现版本已经解决了NFSv2的许多不足之处，但是大文件支持（64位文件大小和偏移量）这一紧迫的问题还没有解决。这成为迪吉多公司的一个痛点，他们当时推出64位版本的Ultrix，以支持其新推出的64位RISC处理器Alpha 21064。在引入NFSv3时厂商们正在越来越多的支持TCP作为传输层协议。当时有些厂商已经在NFS version 2支持TCP做为传输层，Sun Microsystems 在发布NFSv3时也增加了将TCP作为传输层的支持。使用TCP做传输层使得NFS跨越 WAN 成为可能，并且可以突破 UDP 传输大小8K的限制，使用更大的读写数据单元。

#### 1.1.3. NFSv4

NFSv4协议（RFC 3010，2000年12月；更新版 RFC 3530，2003年4月），借鉴了AFS（Andrew File System）和SMB/CIFS（Server Message Block）的特性，主要做了如下改进：性能提升，强制安全策略，引入有状态的协议。从NFSv4开始，协议的实现/开发工作不再是由SUN公司主导开发，而是改为由互联网工程任务组（IETF）开发。

#### 1.1.4. NFSv4.1
NFSv4.1（RFC 5661，2010年1月）旨在为并行访问可横向扩展的集群服务（pNFS扩展）提供协议支持。

#### 1.1.5. NFSv4.2
NFSv4.2 于2016年发布。

#### 1.1.6. 其他扩展

WebNFS，一个NFSv2 v3的扩展，使得用户可以方便的通过网页浏览器与NFS服务端交互，且不受防火墙限制。在2007年，SUN公司开源了WebNFS客户端的实现。

各种NFS相关的外挂／捆绑协议：

   * 字节区间建议锁网络锁定管理（Network Lock Manager，缩写 NLM）协议（支持 UNIX System V 文件锁定 APIs）。
   * 远程配额记录（RQUOTAD）协议；使NFS用户可以查看服务端数据存储配额。
   
NFS over RDMA 是 NFS 对远程直接存储器访问（RDMA）协议的适配，就是将默认的传输层协议 TCP 替换为 RDMA。

### 1.2. 平台

NFS 通常用在 Unix 操作系统上（比如 Solaris、AIX及HP-UX）和其他 类Unix 的操作系统（例如 Linux 及 FreeBSD）。同时在其他一些操作系统也提供了NFS实现，例如经典的 Mac OS、OpenVMS、Microsoft Windows、OS/2、Novell NetWare 还有 IBM AS/400。可选的远程文件访问协议还有服务器消息块（SMB， 或 CIFS）、 苹果归档协议（AFP）、NetWare核心协议（NCP）和 OS/400 文件服务器文件系统（QFileSvr.400）。

在Microsoft Windows系统上 SMB 和 NetWare核心协议（NCP）的使用比 NFS 更广泛；在Apple Macintosh 操作系统上则 AFP 的使用更广泛；而在 AS/400 系统上 QFileSvr.400 更为常用。Haiku 在2013年3月添加了 NFSv4 支持（作为Google 编码夏季项目的一部分）。

### 1.3. 典型实现

假设一个Unix风格的场景，其中一台计算机（客户端）需要访问存储在其他机器上的数据（NFS 服务器）：

1. 服务端实现 NFS 守护进程，默认运行 nfsd，用来使得数据可以被客户端访问。
2. 服务端系统管理员可以决定哪些资源可以被访问，导出目录的名字和参数，通常使用 /etc/exports 配置文件 和 exportfs 命令。
3. 服务端安全-管理员保证它可以组织和认证合法的客户端。
4. 服务端网络配置保证可以跟客户端透过防火墙进行协商。
5. 客户端请求导出的数据，通常调用一个 mount 命令。
6. 如果一切顺利，客户端的用户就可以通过已经挂载的文件系统查看和访问服务端的文件了。

提醒：NFS自动挂载可以通过—可能是 /etc/fstab 或者自动安装管理进程。

## 2. 操作案例

+----------------------+                      |                       +-------------------------+
| [    NFS Server    ]  |192.168.1.101  | 192.168.1.133  |    [    NFS Client    ]    |
|    server01             +-----------------+------------------+        client01             |
|                              |                                                |                                 |
+----------------------+                                               +-------------------------+


### 2.1. 配置一个只读的NFS共享

#### 2.1.1. 配置NFS服务器端

```shell-session
[root@server01 ~]# yum -y install nfs-utils

# [root@server01 ~]# vi /etc/idmapd.conf
# line 5: uncomment and change to your domain name
# Domain = server01

[root@server01 ~]# mkdir /nfs_share
[root@server01 ~]# cd /nfs_share/
[root@server01 nfs_share]# echo "hello nfs on server01" > nfs_test.txt

[root@server01 ~]# vim /etc/exports
# write settings for NFS exports
/nfs_share    192.168.1.0/24(ro)

[root@server01 ~]# systemctl enable --now rpcbind  nfs-server

```

#### 2.1.2. 客户端访问NFS服务器

```shell-session
[root@client01 ~]# yum -y install nfs-utils


[root@client01 ~]# systemctl start rpcbind
[root@client01 ~]# systemctl enable rpcbind


[root@client01 ~]# showmount -e 192.168.1.101
Export list for 192.168.1.101:
/nfs_share 192.168.1.0/24

[root@client01 ~]# mount  -t nfs 192.168.1.101:/nfs_share   /mnt
# if you'd like to mount with NFSv3, add '-o vers=3' option

[root@client01 ~]# df -h  |grep  nfs
192.168.1.101:/nfs_share   17G  1.7G   16G   10% /mnt

[root@client01 ~]# mount |grep  nfs_share
192.168.1.101:/nfs_share on /mnt type nfs4 (rw,relatime,vers=4.1,rsize=131072,wsize=131072,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.1.133,local_lock=none,addr=192.168.1.101)

[root@client01 ~]# cd /mnt
[root@client01 mnt]# ls
nfs_test.txt
[root@client01 mnt]# cat nfs_test.txt 
hello nfs on server01

[root@client01 mnt]# touch abc.txt
touch: 无法创建"abc.txt": 只读文件系统

```

### 2.2. 配置一个读写的NFS共享

#### 2.2.1. 配置NFS服务器端

```shell-session
[root@server01 /]# vim /etc/exports
/nfs_share    192.168.1.0/24(rw)

[root@server01 /]# chmod  777 /nfs_share/

[root@server01 /]# exportfs -av
exporting 192.168.1.0/24:/nfs_share
```


#### 2.2.2. 客户端访问测试

```shell-session
[root@client01 mnt]# touch abc.txt
[root@client01 mnt]# ll abc.txt 
-rw-r--r--. 1 nfsnobody nfsnobody 0 9月  13 19:07 abc.txt
```

### 2.3. NFS用户映射关系

1. 对于普通用户，NFS默认保留文件的拥有者身份（uid）。Linux系统对于用户的识别是通过uid来完成，那么在客户端和服务器端，同一个uid对应的用户名可能一样，也可能不一样，或者不存在。
2. 对于root用户，NFS默认将其映射为nfsnobody。
3. 可以通过配置，取消nfs对于root用户的匿名映射。
4. 可以通过配置，将所有的用户都映射成为某个指定的用户。


#### 2.3.1. 对1的测试
```shell-session
[demo@client01 ~]$ touch  /mnt/demo_test.txt
[demo@client01 ~]$ ll /mnt/demo_test.txt 
-rw-rw-r--. 1 demo demo 0 9月  13 19:14 /mnt/demo_test.txt

[root@server01 /]# ll /nfs_share/demo_test.txt 
-rw-rw-r-- 1 1000 zhangsan 0 9月  13 19:14 /nfs_share/demo_test.txt

```

#### 2.3.2. 对3的测试
```shell-session
/nfs_share    192.168.1.0/24(rw,no_root_squash)
```

#### 2.3.3. 对4的测试
```shell-session
/nfs_share    192.168.1.0/24(rw,all_squash,anonuid=1001,anongid=1001)
```

#### 2.3.4. nfs读写权限和用户映射参考

可以使用man exports查看。

```
EXAMPLE
       # sample /etc/exports file
       /               master(rw) trusty(rw,no_root_squash)
       /projects       proj*.local.domain(rw)
       /usr            *.local.domain(ro) @trusted(rw)
       /home/joe       pc001(rw,all_squash,anonuid=150,anongid=100)
       /pub            *(ro,insecure,all_squash)
       /srv/client01        -sync,rw server @trusted @external(ro)
       /foo            2001:db8:9:e54::/64(rw) 192.0.2.0/24(rw)
       /build          buildhost[0-9].local.domain(rw)
```

### 2.4. /etc/exports基本选项

| 选项              | 描述                                                                                                                                                                  |
| :--------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| rw               | 允许客户端读写请求。                                                                                                                                                     |
| ro               | 只读。                                                                                                                                                                 |
| sync             | 同步，仅在将更改提交到稳定存储之后才回复请求。(默认值)                                                                                                                        |
| async            | 异步，在将请求所做的任何更改提交到稳定存储之前回复该请求。                                                                                                                     |
| secure           | 此选项要求请求源自小于 IPPORT_RESERVED （1024） 的 Internet 端口。（默认值）                                                                                                 |
| insecure         | 允许所有的端口。                                                                                                                                                        |
| subtree_check    | 此选项启用子树检查。（默认值）                                                                                                                                             |
| no_subtree_check | 此选项禁用子树检查，这具有轻微的安全隐患，但在某些情况下可以提高可靠性。                                                                                                          |
| root_squash      | 将请求从 uid/gid 0 映射到匿名的 uid/gid。请注意，这不适用于可能同样敏感的任何其他 uid 或 gid，例如用户 bin 或组工作人员。                                                           |
| no_root_squash   | 关闭root映射。此选项主要对无磁盘客户机有用。                                                                                                                                |
| all_squash       | 将所有 uid 和 gid 映射到匿名用户。适用于 NFS 导出的公共 FTP 目录、新闻目录等。                                                                                                 |
| no_all_squash    | 关闭所有普通用户（不包含root）映射。（默认值）                                                                                                                               |
| anonuid=UID      | 这些选项显式设置匿名帐户的 uid 和 gid。此选项主要用于 PC/NFS 客户端，您可能希望所有请求都显示为来自一个用户。例如，请考虑以下示例部分中 /home/joe 的导出条目，该条目将所有请求映射到 uid 150。 |
| anongid=GID      | 见上文（anonuid=UID）                                                                                                                                                  |


### 2.5. 配置NFS开机自动挂载

手动mount NFS文件系统，当系统重新启动后，NFS文件系统将会被卸载掉。配置NFS文件系统开机自动挂载，有两种方式：
1. fstab
2. autofs

#### 2.5.1. 使用fstab

```shell-session
[root@client01 ~]# vi /etc/fstab

# add like follows to the end
192.168.1.101:/nfs_share   /mnt        nfs     defaults     0  0
```

#### 2.5.2. 配置auto-mounting

```shell-session
[root@client01 ~]# yum -y install autofs

[root@client01 ~]# systemctl enable --now autofs.service

[root@client01 ~]# cd /net/
[root@client01 net]# ls
[root@client01 net]# cd 192.168.1.101
[root@client01 192.168.1.101]# ls
nfs_share
[root@client01 192.168.1.101]# ls nfs_share/
abc.txt  demo_test.txt  nfs_demo.txt  nfs_test.txt  root2.txt  root.txt

[root@client01 ~]# df  -hT |grep nfs
192.168.1.101:/nfs_share nfs4       17G  1.7G   16G   10% /net/192.168.1.101/nfs_share
```

```shell-session
[root@client01 ~]# vi /etc/auto.master
# add follows to the end
/-    /etc/auto.mount

[root@client01 ~]# vi /etc/auto.mount
# create new : [mount point] [option] [location]
/mntdir -fstype=nfs,rw  192.168.1.101:/nfs_share

[root@client01 ~]# mkdir /mntdir

[root@client01 ~]# systemctl restart autofs

# move to the mount point to make sure it normally mounted
[root@client01 ~]# cd /mntdir
[root@client01 mntdir]# ls
abc.txt  demo_test.txt  nfs_demo.txt  nfs_test.txt  root2.txt  root.txt
[root@client01 mntdir]# df -hT |grep  nfs
192.168.1.101:/nfs_share nfs4       17G  1.7G   16G   10% /mntdir
```




## 3. 实践任务

配置一个NFS服务器：

1. NFS的共享目录/nfs/public ， 实现所有用户可写，并权限映射到zhangsan.
1. NFS的共享目录/nfs/data, 实现root用户可写，其他用户只读，并不映射为匿名用户。
1. 配置客户端使用fstab实现重启后自动挂载nfs共享到/data目录。
1. 使用autofs实现访问时自动挂载nfs共享到/pub目录。

## 4. 扩展指引

1. NFS如何固定服务的随机端口？

2. NFS配置文件的帮助文档`man exports`。



