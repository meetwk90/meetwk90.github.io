---
layout: post
title:  DigitalOcean + Ubuntu+ WordPress 个人建站全记录
date:   2015-10-18
excerpt: "一直想做一个个人建站的全记录，苦于没有什么时间，自己又太懒。刚好最近需要做一个克莱斯勒产品线展示作品，让我有机会把这个过程记录下来，分享给大家。"
tag:
- DigitalOcean
- ubuntu
- WordPress
- Walter
comments: true
---

一直想做一个个人建站的全记录，苦于没有什么时间，自己又太懒。刚好最近需要做一个克莱斯勒产品线展示作品，让我有机会把这个过程记录下来，分享给大家。

主机选择 DigitalOcean，系统是 Ubuntu。

<a href="https://www.digitalocean.com/?refcode=945e7487a6b0" target="_blank">https://www.digitalocean.com/?refcode=945e7487a6b0</a> 这个链接是我在 DigitalOcean 的 referral，直接注册可以获得10刀的 Coupon 哦。

注册成功之后用邮件激活，个人资料的填写等在此不表。需要注意的是在建立你的第一个 Droplet 之前，需要认证你的信用卡信息。因此你至少需要一张双币或全币信用卡，用来支付之后产生的租赁费用。

以上搞定之后系统会生成一个 ticket，DO的工作人员会进行人工审核。DO的工作效率还是值得表扬的，大概几分钟呢就会回复你审核通过与否了。

之后我们就开始正式开始建站的部分啦。点击网站右上角，开始建立你的第一个“小水滴”。

<img src="http://o8c8x14zj.bkt.clouddn.com/Create-Droplet.png" alt="Create Droplet" />

键入主机名称

<img src="http://o8c8x14zj.bkt.clouddn.com/Droplet-Hostname.png" alt="Droplet Hostname" />

依个人需求选择包月套餐

<img src="http://o8c8x14zj.bkt.clouddn.com/Select-Size.png" alt="Select Size" />

选择机房的 location，你可以到<a href="http://speedtest-sfo1.digitalocean.com/" target="_blank">http://speedtest-sfo1.digitalocean.com/</a> 测试速度，选择最快最稳定的那个接入点。一般建议选择三藩市。

<img src="http://o8c8x14zj.bkt.clouddn.com/Select-Region.png" alt="Select Region" />

选择 Ubuntu 作为我们的操作系统

<img src="http://o8c8x14zj.bkt.clouddn.com/Select-Image.png" alt="Select Image" />

旁边的 Applications 选项卡下可以选择 WordPress on 14.04 作为已经配置好的 WP 环境，需要至少30GB的磁盘空间，不过我没有选择过这个，大家可以试试看。

下面的 Available Settings 及 SSH Keys 属于可选项目，如果大家有兴趣可以研究下。特别是 SSH，可以让你的主机更安全。在此按下不表，毕竟我要做的是一个临时项目，我就不麻烦自己啦：）

都选择完毕后点击<img src="http://o8c8x14zj.bkt.clouddn.com/Create-Droplet_2.png" alt="Create Droplet_2" />

DO 的效率非常高，大概等待几分钟，就提示建立完成啦。

<img src="http://o8c8x14zj.bkt.clouddn.com/Chrysler.png" alt="Chrysler" />

在这个界面可以看到主机的基本信息，包括 IP 地址，Console 接入，和重置密码等。

同时你应该也收到啦DO发送给你到邮件，里面包含了你的登陆账号和密码。

你可以选择直接在DO的 Console Access 接入你的主机，但是体验并不好。如果你使用的是 Mac 或 Linux 的操作系统，也可以在 Terminal 里用 SSH 直接接入。如果使用的是 Windows 的话，建议使用 Putty 这个开源软件。下载地址：<a href="http://www.putty.org/" target="_blank">http://www.putty.org/</a> (居然被墙掉了。请自行翻墙，或寻找其他下载源。)

安装完成后输入登陆账号（root），IP 地址，端口号等信息

<img src="http://o8c8x14zj.bkt.clouddn.com/Putty.png" alt="Putty" />

第一次登陆需要修改你等密码。

好了接下来就是部署 WordPress 安装环境的过程了。

先更新一下包：

<code>sudo apt-get install update</code>

配置 lamp:

<code>sudo apt-get install apache2 mysql-server php5</code>

安装 MySQL 的过程需要创建数据库密码。

安装完成后验证一下。先启动 apache:

<code>service apache2 start</code>

浏览器中输入你的主机IP地址，如果 apache 启动成功会显示"It works!"。

<img src="http://o8c8x14zj.bkt.clouddn.com/Apache.png" alt="Apache" />

验证 MySQL:

<code>mysql -V</code>

验证 PHP:

<code>php --version</code>

安装 phpmyadmin:

<code>sudo apt-get install phpmyadmin</code>

安装过程中需要创建 phpmyadmin 的管理密码，以及输入 MySQL 的管理密码。

由于 phpmyadmin 默认创建在 /usr/share 下，我们要访问的话需要建立一个软链接：

<code>sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin</code>

这样我们就可以在浏览器里访问 phpmyadmin 了。

<img src="http://o8c8x14zj.bkt.clouddn.com/phpmyadmin.png" alt="phpmyadmin" />

登陆之后创建 wordpress 数据库： <img src="http://o8c8x14zj.bkt.clouddn.com/mysql.png" alt="mysql" />

以上步骤完成后，WordPress 所必需的环境就配置好了。下面我们开始安装 WordPress。

到官方网站 <a href="https://cn.wordpress.org/" target="_blank">https://cn.wordpress.org/</a> 下载 .tar.gz 文件。

我们需要一个ftp工具把压缩文件传送到主机上。Windows 下我使用的是 WinSCP，官方下载地址 <a href="https://winscp.net/eng/download.php" target="_blank">https://winscp.net/eng/download.php</a> 如果你使用的是其他操作系统，找到对应的 ftp 工具就可以了。

上传完成后我们需要把压缩文件解压到 /var/www/html 下。如果直接使用命令解压过去，当我们要访问主页时就需要在域名或 IP 后面加上 /wordpress 才访问的到。

如果想要实现输入域名或IP直接访问主页的效果，需要把 wordpress 文件夹下的东西全部提取到 /var/www/html 下：

<code>sudo tar xzvf wordpress-4.3.1-zh_CN.tar.gz</code>
<code>sudo cp -Rf wordpress/* /var/www/html/</code>

现在再尝试访问一下你的IP地址，安装程序应该就可以开始了。

<img src="http://o8c8x14zj.bkt.clouddn.com/WordPress-Setup.png" alt="WordPress-Setup" />

如果发现输入IP之后还是显示 Apache 的页面，需要把 /var/www/html 下的 index.html 手动删除：

<code>sudo rm -f /var/www/html/index.html</code>

点击开始后填写之前创建好的数据库用户名和密码等。

再到下一步可能会得到一个没有权限写入的提示：

<img src="http://o8c8x14zj.bkt.clouddn.com/Deny.png" alt="Deny" />

更改权限：

<code>sudo chmod -R 777 /var/www/html</code>

如果你实在担心安全问题，也可以按照上面的提示手动创建 wp-config.php 文件。

继续下去：

<img src="http://o8c8x14zj.bkt.clouddn.com/Welcome.png" alt="Welcome" />

<img src="http://o8c8x14zj.bkt.clouddn.com/Success.png" alt="Success" />

成功啦！

点击登陆进入仪表盘 Dashboard。刚安装完成就发现更新需求了-_-|||

<img src="http://o8c8x14zj.bkt.clouddn.com/Update.png" alt="Update" />

在升级插件的时候，出现了这种情况：

<img src="http://o8c8x14zj.bkt.clouddn.com/Plugin.png" alt="Plugin" />

我们需要通过修改 /var/www/html 下的 wp-config.php 的方法来解决。在脚本文件的最后添加以下代码：

<code>
define('FS_METHOD', 'direct');
define('FS_CHMOD_DIR', 0777);
define('FS_CHMOD_FILE', 0777);
</code>

然后就可以安装更新插件或主题啦。

好啦，剩下的部分就是调试页面样式和编辑自己的 Blog 了。Let's start!
