---
layout: post
title: "修改 WordPress 上传文件大小(Maximum upload file size)限制"
date: 2016-04-27
excerpt: "解决通过修改 php.ini 里的参数也无法解除上传文件大小限制的方案。"
tag:
- ubuntu
- WordPress
- NGINX
- PHP
- Walter
comments: true
---

本方案解决了即使修改了 php.ini 及 NGINX（或 Apache）的相关参数，也无法修改上传文件大小限制的问题。

Google 得到的教程里，基本上提到需要修改 php.ini 里的三个相关参数 <code>post_max_size</code>, <code>upload_max_filesize</code>, <code>memory_limit</code>, 以及 NGINX 配置文件里的 <code>client_max_body_size</code>

但有事发现即使这些参数已经做过相应的修改，限制也没有办法解除。

要看修改结果有没有最终成功，最直观的方法是查看 <code>phpinfo();</code> 里的 Core 目录。如果这个目录下面的 post_max_size, uploade_max_filesize, memory_limit 没有跟随修改而变化，那么修改就是没有成功的。

解决方法是修改 .conf 文件来覆盖 php.ini 的配置结果。这个文件一般跟 php.ini 文件在同一目录下。比如我通过查看 phpinfo 信息得知配置文件是 /etc/php5/fpm/ 下的 php.ini，那么我们要修改的文件即为同目录下的 php5-fpm.conf。

在文件最后添加以下配置信息即可（根据自己需要修改大小）。

    php_value[memory_limit] = 96M
    php_value[max_execution_time] = 120
    php_value[max_input_time] = 300
    php_value[post_max_size] = 25M
    php_value[upload_max_filesize] = 25M

>其中 max_execution_time 和 max_input_time 分别代表 php 程序执行的最大时间和文件上传的最大时间。
