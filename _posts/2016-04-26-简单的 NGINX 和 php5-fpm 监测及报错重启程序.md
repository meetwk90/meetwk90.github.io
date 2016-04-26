---
layout: post
title: "简单的 NGINX 和 php5-fpm 监测及报错重启程序"
date: 2016-04-26
excerpt: "php5-fpm 经常出错假死，导致网站报错502 Bad Gateway 的解决方案。"
tag:
- ubuntu
- PHP
- NGINX
- crontab
- Walter
comments: true
---

<code>sudo crontab -e</code> 编辑 crontab 脚本。

<code>
## Auto-restart PHP when it's returning errors
#
# Make sure that http://localhost/test.php is an actual PHP script. If it starts returning
# 500 errors, restart the PHP-FPM service
*   *   *   *   *    /usr/bin/curl --head -sf http://localhost/test.php -o /dev/null || (/usr/sbin/service php5-fpm restart && /usr/sbin/service nginx restart)
</code>

加入以上代码，保存退出。

确保 test.php 是真实可执行的 PHP 程序。我是使用<code>phpinfo();</code>作为 test.php 的内容的。

这个脚本是通过 curl 获得 http head 信息来触发，一旦监测到错误，就自动重启 php5-fpm 和 NGINX。
