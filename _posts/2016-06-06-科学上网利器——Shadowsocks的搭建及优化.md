---
layout: post
title:  "科学上网利器——Shadowsocks的搭建、配置及优化"
date:   2016-06-06
excerpt: "你懂的。"
tag:
- Walter
- Shadowsocks
- Ubuntu
- VPN
---

# Shadowsocks 的原理 #

简单理解的话，Shadowsocks 是将以前通过 SSH 创建的 Socks5 协议拆开成 Server 端和 Client 端。

原理图如下，基本上和利用 SSH Tunnel 大致类似:
<img src="http://o8c8x14zj.bkt.clouddn.com/Shadow.png" alt="Shadowsocks" />

1. PC客户端发出请求基于 Socks5 协议跟 SS-Local 端进行通讯，由于这个 SS-Loacl 一般是本机或路由器等局域网的其他机器，不经过 GFW，所以解决 GFW 通过特征分析进行干扰的问题。
2. SS-Local 和 SS-Server 两端通过多种可选的加密方法进行通讯，经过 GFW 的时候因为是常规 TCP 包，没有明显特征码 GFW 也无法对通讯数据进行解密，因此通讯放行。
3. SS-Server 将收到的加密数据进行解密，还原初始请求，再发送到用户需要访问的服务网站，获取响应原路再返回 SS-Local，返回途中依然使用了加密，使得流量是普通的 TCP 包，并成功穿过 GFW 防火墙。

因此，Shadowsocks 的优点在于它解决了 GFW 通过分析流量特征从而干扰的问题，这是它优于 SSH 和 VPN 翻墙的地方。

缺点也依然明显，需要一点点技术和资源（墙外 VPS 服务器）来搭建 Shadowsocks 服务。

# Shadowsocks 的搭建 #

首先，我假定你已经拥有了自己的 VPS（要在国外或香港的）。如果你没有，go back to [DigitalOcean + Ubuntu+ WordPress 个人建站全记录](/DigitalOcean-+-Ubuntu+-WordPress-个人建站全记录/) and get one!

以下是 Shadowsocks 搭建步骤。

## 服务端 ##

### 安装 ###

#### Dibian / Ubuntu: ####

<code>
sudo apt-get install python-pip
pip install shadowsocks
</code>

#### CentOS: ####

<code>
yum install python-setuptools && easy_install pip
pip install shadowsocks
</code>

#### Windows: ####

不建议使用 Windows 服务器。

### 使用 ###

#### 方法 1 ####

运行 Shadowsocks 服务端：

<code>sudo ssserver -p port -k password -m encryption</code>

如果要后台运行：

<code>sudo ssserver -p port -k password -m encryption --user nobody -d start</code>

如果要停止：

<code>sudo ssserver -d stop</code>

如果要检查日志：

<code>sudo less /var/log/shadowsocks.log</code>

用<code>-h</code>查看所有参数。

#### 方法 2 ####

或者你可以使用配置文件（Config File）进行配置。

创建一个配置文件<code>/etc/shadowsocks.json</code>

Example:
{% highlight json %}
{
    "server": "my_server_ip",
    "sever_port": 8388,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "mypassword",
    "timeout": 300,
    "method": "aes-256-cfb",
    "fast_open": false
}
{% endhighlight %}

键值解释：
<table>
    <thead>
        <tr>
            <td>Name</td>
            <td>Explanation</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>server</td>
            <td>你的服务器地址</td>
        </tr>
        <tr>
            <td>server_port</td>
            <td>服务器的端口号</td>
        </tr>
        <tr>
            <td>local_address</td>
            <td>本地地址</td>
        </tr>
        <tr>
            <td>local_port</td>
            <td>本地端口号</td>
        </tr>
        <tr>
            <td>password</td>
            <td>加密用的密码</td>
        </tr>
        <tr>
            <td>timeout</td>
            <td>以秒为单位</td>
        </tr>
        <tr>
            <td>method</td>
            <td>默认为："aes-256-cfb"</td>
        </tr>
        <tr>
            <td>fast_open</td>
            <td>使用 TCP_FASTOPEN, true / false</td>
        </tr>
        <tr>
            <td>workers</td>
            <td>workers 的数量，Linux/Unix 可用</td>
        </tr>
    </tbody>
</table>

前台运行：

<code>ssserver -c /etc/shadowsocks.json</code>

后台运行：

<code>ssserver -c /etc/shadowsocks.json -d start</code>

停止运行：

<code>ssserver -c /etc/shadowsocks.json -d stop</code>

## 客户端 ##

### Windows ###

#### 功能 ####

1. 系统代理设置
2. PAC 模式和全局模式
3. GFWList 和用户规则
4. 支持 HTTP 代理
5. 支持多服务器切换
6. 支持 UDP 代理

#### 下载 ####

[3.0版本下载](http://o8c8x14zj.bkt.clouddn.com/Shadowsocks-3.0.zip)

#### 基本使用 ####

1. 在任务栏找到 Shadowsocks 图标
2. 在<code>服务器</code>菜单添加自己的服务器
3. 选择<code>启用系统代理</code>来启用。请禁用浏览器里的代理插件，或把它们设置为使用系统代理。
4. 除了设为系统代理，你也可以直接自己配置浏览器代理。在 SwitchOmega 中把代理设置为 SOCKS5 或 HTTP 的 127.0.0.1：1080。这个1080端口可以在服务器设置中设置。

#### PAC ####

1. 可以编辑 PAC 文件来修改 PAC 设置。Shadowsocks 会监听文件变化，修改后会自动生效。
2. 你也可以从 [GFWList](https://github.com/gfwlist/gfwlist) (由第三方维护)更新 PAC 文件。
3. 你也可以使用在线 PAC URL。

#### 服务器自动切换 ####

1. 负载均衡：随机选择已添加的服务器
2. 高可用：根据延迟和丢包率自动选择已添加的服务器
3. 累计丢包率：通过定时 ping 来测速和选择。如果要使用本功能，请打开菜单里的<code>统计可用性</code>。

### OS X ###

OS X 10.8+ 可用。

#### 下载 ####

[2.6.3版本下载](http://o8c8x14zj.bkt.clouddn.com/ShadowsocksX-2.6.3.dmg)

#### 基本使用 ####

基本等同 Windows 版。

### Android ###

#### 下载 ####

[2.9.10版本 apk 下载](http://o8c8x14zj.bkt.clouddn.com/shadowsocks-nightly-2.9.10.apk)

[Google Play](https://play.google.com/store/apps/details?id=com.github.shadowsocks)

### iOS ###

iOS 系统由于种种限制，不越狱使用的话目前只能实现通过 Web 浏览器的方式翻墙，Gloable 模式限制过多，不推荐使用。

#### 下载 ####

[App Store](https://itunes.apple.com/us/app/shadowsocks/id665729974?ls=1&mt=8)

# 优化 shadowsocks #

如果你在自己的日志中经常发现<code>error: too many open files</code>，下面的优化配置也许能帮上忙。

Dibian 7 / Ubuntu：

创建<code>/etc/sysctl.d/local.conf</code>并写入以下代码：

    # max open files
    fs.file-max = 51200
    # max read buffer
    net.core.rmem_max = 67108864
    # max write buffer
    net.core.wmem_max = 67108864
    # default read buffer
    net.core.rmem_default = 65536
    # default write buffer
    net.core.wmem_default = 65536
    # max processor input queue
    net.core.netdev_max_backlog = 4096
    # max backlog
    net.core.somaxconn = 4096

    # resist SYN flood attacks
    net.ipv4.tcp_syncookies = 1
    # reuse timewait sockets when safe
    net.ipv4.tcp_tw_reuse = 1
    # turn off fast timewait sockets recycling
    net.ipv4.tcp_tw_recycle = 0
    # short FIN timeout
    net.ipv4.tcp_fin_timeout = 30
    # short keepalive time
    net.ipv4.tcp_keepalive_time = 1200
    # outbound port range
    net.ipv4.ip_local_port_range = 10000 65000
    # max SYN backlog
    net.ipv4.tcp_max_syn_backlog = 4096
    # max timewait sockets held by system simultaneously
    net.ipv4.tcp_max_tw_buckets = 5000
    # turn on TCP Fast Open on both client and server side
    net.ipv4.tcp_fastopen = 3
    # TCP receive buffer
    net.ipv4.tcp_rmem = 4096 87380 67108864
    # TCP write buffer
    net.ipv4.tcp_wmem = 4096 65536 67108864
    # turn on path MTU discovery
    net.ipv4.tcp_mtu_probing = 1

    # for high-latency network
    net.ipv4.tcp_congestion_control = hybla

    # for low-latency network, use cubic instead
    # net.ipv4.tcp_congestion_control = cubic

然后:

<code>sysctl --system</code>

Warning: 不要打开<code>net.ipv4.tcp_tw_recycle</code>!!!

如果你使用了 [Supervisor](https://github.com/shadowsocks/shadowsocks/wiki/%E7%94%A8-Supervisor-%E8%BF%90%E8%A1%8C-Shadowsocks) (从 Shadowsocks 2.6版本开始，你可以直接在后台运行 Shadowsocks，无需 Supervisor)，确保<code>ulimit -n 51200</code>已写入<code>/etc/default/supervisor</code>。一旦写入后，重启 Supervisor (<code>service stop supervisor && service start supervisor</code>)。

> 部分内容节选、翻译自 https://github.com/shadowsocks
