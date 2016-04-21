---
layout: post
title:  "Ubuntu 下 MySQL 密码的重置"
date:   2015-11-24
excerpt: "忘记 MySQL 密码的情况下绕过密码验证强行登录后进行密码修改。"
tag:
- ubuntu
- MySQL
- Walter
comments: true
---

<code>sudo vi /etc/mysql/my.cnf</code>
，在[mysqld]段中加入一行:
<code>skip-grant-tables</code>

重启 MySQL：
<code>sudo service mysql restart</code>

用空密码进入mysql管理命令行
<code>sudo mysql -u root -p mysql</code>
(进入MySQL,或者用use MySQL指令)

把密码重置为123：
{% highlight sql %}
UPDATE user SET password=PASSWORD(“123″) WHERE user=’root’;
(注意，如果是表中没有的用户名，使用insert)
{% endhighlight %}

quit，退出数据库管理。

把刚才加入的那一行<code>skip-grant-tables</code>注释或删除

<code>sudo service mysql restart</code>
OK，搞定！
