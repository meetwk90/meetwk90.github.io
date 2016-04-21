---
layout: post
title:  "Ubuntu 下增加 Swap 分区"
date:   2016-01-31
excerpt: "为 VPS 增加 Swap 分区的方法。"
tag:
- ubuntu
- swap
- Walter
- VPS
comments: true
---

今天我的 VPS 又莫名其妙的宕机，具体表现为 MySQL 经常挂掉，重启 MySQL 的时候 Fail 掉开不起来。

Google之后得知有许多人跟我有同样的困扰，也大多是 WordPress 用户，解决办法是为 VPS 增加 Swap 分区。

好了开始介绍 Ubuntu 系统下增加 Swap 分区的方法。

#### 检查系统 Swap 分区信息 ####

在我们开始分区之前，我们需要查看系统是否已经有了 Swap 分区，是否有足够空间分给 Swap。我们可以分出一个或几个 Swap 分区来，但实际上大多数情况下一个 Swap 就足够了。

我们通过这种方式来查看 Swap 分区情况：
<pre>sudo swapon -s</pre>

>Filename&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Type&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Size&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Used&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Priority

如果你得到的像以上显示的那样的空表，那么你的系统中是没有建立Swap分区的。

另一种查看的方法是使用<code>free</code>命令，<code>free</code>是用来查看系统内存使用的，我们通过以下命令来查看：
<pre>free -m</pre>

<pre>
             total       used       free     shared    buffers     cached
Mem:          3953        154       3799          0          8         83
-/+ buffers/cache:         62       3890
Swap:            0         0          0
</pre>

#### 查看硬盘空间大小 ####

输入：
<pre>df -h</pre>

<pre>
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda         59G  1.3G   55G   3% /
none            4.0K     0  4.0K   0% /sys/fs/cgroup
udev            2.0G   12K  2.0G   1% /dev
tmpfs           396M  312K  396M   1% /run
none            5.0M     0  5.0M   0% /run/lock
none            2.0G     0  2.0G   0% /run/shm
none            100M     0  100M   0% /run/user
</pre>

很好，我们有55G的硬盘空间可以使用。

#### 创建 Swap 文件 ####

我们将在根目录(/)下创建一个叫做<code>swapfile</code>的文件。我们通过使用<code>fallocate</code>程序创建它。

<pre>sudo fallocate -l 4G /swapfile</pre>

这条命令立马生效，我们可以通过输入下面这行命令来确认：
<pre>ls -lh /swapfile</pre>

<pre>-rw-r--r-- 1 root root 4.0G Apr 28 17:19 /swapfile</pre>

好了，我们已经得到了我们想要的Swap文件。

#### 激活 Swap 文件 ####

我们需要让系统格式化 Swap 文件，并激活它。

在这之前，我们需要调整文件权限来加固安全性能。

<pre>sudo chmod 600 /swapfile</pre>

验证：

<pre>ls -lh /swapfile</pre>

<pre>-rw------- 1 root root 4.0G Apr 28 17:19 /swapfile</pre>

好了，现在它变得更安全啦。下面我们让系统来创建Swap分区：

<pre>sudo mkswap /swapfile</pre>

<pre>
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=e2f1e9cf-c0a9-4ed4-b8ab-714b8a7d6944
</pre>

激活：

<pre>sudo swapon /swapfile</pre>

验证：

<pre>sudo swapon -s</pre>

<pre>
Filename                Type        Size    Used    Priority
/swapfile               file        4194300 0       -1
</pre>

<pre>free -m</pre>

<pre>
             total       used       free     shared    buffers     cached
Mem:          3953        101       3851          0          5         30
-/+ buffers/cache:         66       3887
Swap:         4095          0       4095
</pre>

现在，Swap 分区就创建成功了！

#### 使 Swap 分区永久存在 ####

目前做到这步，Swap 分区会一直存在知道我们将主机重启，主机并不会在重启后自动激活 Swap 文件。通过修改<code>fstab</code>文件我们可以做到这点。

编辑<code>fstab</code>：

<pre>sudo nano /etc/fstab</pre>

在文档的最后，我们新增一行：

<pre>/swapfile   none    swap    sw    0   0</pre>

保存退出即可。
