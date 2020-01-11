---
title: light frequency authenticate
date: 2019-07-05 11:09:43
tags:
- lightFre
categories:
- light-frequency-authenticate
---
<blockquote class="blockquote-center">基于可见光的无线网络认证</blockquote>
<!--more-->

2019-07-05

## 802.1x认证过程：

1. 当用户有访问网络需求时打开802.1X客户端程序，输入已经申请、登记过的用户名和密码，发起连接请求（EAPOL-Start报文）。此时，客户端程序将发出请求认证的报文给设备端，开始启动一次认证过程。

2. 设备端收到请求认证的数据帧后，将发出一个请求帧（EAP-Request/Identity报文）要求用户的客户端程序发送输入的用户名。

3. 客户端程序响应设备端发出的请求，将用户名信息通过数据帧（EAP-Response/Identity报文）发送给设备端。设备端将客户端发送的数据帧经过封包处理后（RADIUS Access-Request报文）送给认证服务器进行处理。

4. RADIUS服务器收到设备端转发的用户名信息后，将该信息与数据库中的用户名表对比，找到该用户名对应的密码信息，用随机生成的一个加密字对它进行加密处理，同时也将此加密字通过RADIUS Access-Challenge报文发送给设备端，由设备端转发给客户端程序。

5. 客户端程序收到由设备端传来的加密字（EAP-Request/MD5 Challenge报文）后，用该加密字对密码部分进行加密处理（此种加密算法通常是不可逆的），生成EAP-Response/MD5 Challenge报文，并通过设备端传给认证服务器。

6. RADIUS服务器将收到的已加密的密码信息（RADIUS Access-Request报文）和本地经过加密运算后的密码信息进行对比，如果相同，则认为该用户为合法用户，反馈认证通过的消息（RADIUS Access-Accept报文和EAP-Success报文）。

7. 设备收到认证通过消息后将端口改为授权状态，允许用户通过端口访问网络。在此期间，设备端会通过向客户端定期发送握手报文的方法，对用户的在线情况进行监测。缺省情况下，两次握手请求报文都得不到客户端应答，设备端就会让用户下线，防止用户因为异常原因下线而设备无法感知。

8. 客户端也可以发送EAPOL-Logoff报文给设备端，主动要求下线。设备端把端口状态从授权状态改变成未授权状态，并向客户端发送EAP-Failure报文。

## 关于WiFidog：
Portal认证，通常也会叫Web认证，未认证用户上网时，设备强制用户登录到特定站点，用户可以免费访问其中的服务。当用户需要使用互联网中的其它信息时，必须在门户网站进行认证，只有认证通过后才可以使用互联网资源。现金很多中国移动CMCC、中国联通、中国电信ChinaNet的WIFI都使用这种认证接入方式。

在OpenWRT上实现Portal认证，实际上早已有解决方案：

1. chillispot，但原维护作者停止更新，被chillispot.info接管继续开发；
2. coova-chilli，它是基于chillispot开发拓展的，功能最为强大；可以去官方看一下Coova-chilli；
3. __wifidog__，前两个由于原维护作者停止更新，笔者也没有深入研究，重点钻研了wifidog，Wifidog也是OpenWRT和DD-WRT中实现Portal比较出名的。但是，Wifidog只是实现AP认证网关，需要配合外部的Portal服务器才能使用，Portal主要是提供认证所需的WEB页面且实现认证计费等的功能。虽然这也有很多商用解决方案，例如wiwiz、wifiap等，但是这些商业解决方案的目标都是盈利，即使可以免费使用，免费账号的功能和权限都受到了很大的限制，例如不能自定义页面，Web认证页面有广告等等。有条件的人可能打算自己搭建Portal服务器，但是看看Wifidog的官方Wiki，对搭建过程实在是难以理解。后来，笔者发现网络上还有一个 __authpuppy方案__ ，官方网站 __www.authpuppy.org__ ，是一个已实现好的Wifidog认证服务器，里面包含各种插件供你使用，官方的安装过程也很简单，如果你懂的HTML和面向对象编程的相关知识且拥有一个服务器，可以自行修改认证页面，使用authpuppy也是一个不错的方案。
4. 如何 __自行编写一个轻量级的Web Portal认证服务器__ ：
 - ### Wifidog的工作原理：
     1. 客户端发出初始化请求，比如访问 www.dqrun.com 。
     2. 网关的防火墙规则将这个请求重定向到本地网关的端口上。这个端口是Wifidog监听的端口。
     3. Wfidog提供一个HTTP重定向回复，重定向到Web认证页面，重定向的Url的Querystring中包含了Gateway的ID，Gateway的FQDN以及其他的信息。
     4. 用户向认证服务器发出认证请求
     ```http://portal_server:port/login_script?
     gw_id=[GatewayID, default: “default”]
     gw_address=[GatewayAddress, internal IP of router]
     gw_port=[GatewayPort, port that wifidog Gateway is listening on]
     url=[user requested url]；
     ```
     5. 网关返回一个（可以是自定义的）splash（也称作“登录”）页面。
     6. 用户提供他的凭据信息，比如用户名和密码。
     7. 成功认证的话，客户端将会被重定向到网关的自己的web页面上，并且带有一个认证凭据（一个一次性的token），内容比如：http://GatewayIP:GatewayPort/wifidog/auth?token=[auth token]；
     8. 用户就是用获取到的凭据访问网关。
     9. 网关去认证服务器询问token的有效性。
     10. 认证服务器确认token的有效性。
     11. 网关发送重定向给客户端，以从认证服务器上获取 成功提示页面，重定向到 http://portal_server:port/portal_script 这个位置。
     12. 认证服务器通知客户请求成功，可以上网了。


## 待完成工作：
__装wifidog__ ，配置:<br>1）监听端口 2)服务器地址 3）5个脚本的地址(login, portal, msg, ping, auth)<br> __配置方法：__远程登陆openwrt: ssh root@192.168.1.1， 然后修改/etc/wifidog.conf文件。

__将笔记本搭建成服务器__

__添加对LED的频率提取功能__

__实现动态调节LED频率,将此功能添加至系统__


--------------------------
2019/07/08

## 路由器刷openwrt系统
恢复出厂：断电后，先按住reset再通电。<br>
登陆192.168.1.1（breed web），先点“恢复出厂”，选择“openwrt”；再点击“更新固件”，选中已下载好的openwrt镜像，进行固件更新。<br>

### 解决路由器联网问题
1. 设置密码：点击`Go to password configuration...`，敲入2遍新的路由器密码，点击页面最下面的 保存执行按钮，密码修改即时生效。 
2. 设置主机名和时区：`System`菜单的`system`进去，主机名改为自己希望的名称，时区设置为`Asia/Shanghai`，保存。
3. __联网设置__ :点击菜单`Network`中的第二项，可以选择2G或5G无线网络连接WIFI，按 `scan`扫描按钮，稍等出现列表中，选择自己(想通过这个路由连接)的WIFI，输入密码，按提交，然后稍等，按保存执行。

## ubuntu16.04编译OpenWrt环境搭建：
1. ubuntu下OpenWrt编译环境需要安装很多组件：<br>
`sudo apt-get install gcc `
`sudo apt-get install g++` 
`sudo apt-get install binutils `
`sudo apt-get install patch `
`sudo apt-get install bzip2 `
`sudo apt-get install flex `
`sudo apt-get install bison `
`sudo apt-get install make `
`sudo apt-get install autoconf `
`sudo apt-get install gettext `
`sudo apt-get install texinfo `
`sudo apt-get install unzip `
`sudo apt-get install sharutils `
`sudo apt-get install subversion `
`sudo apt-get install libncurses5-dev `
`sudo apt-get install ncurses-term `
`sudo apt-get install zlib1g-dev `
`sudo apt-get install subversion `
`sudo apt-get install  git-core`
`sudo apt-get install gawk`
`sudo apt-get install asciidoc`
`sudo apt-get install libz-dev`
当然安装之前最好先更新下组件包：
`sudo apt-get update`
逐个安装...

2. 新建一个openwrt目录，使用命令：
`mkdir openwrt`
`sudo chmod 777 openwrt`
接下来的所有命令都在/openwrt目录下运行

3. 下载OpenWrt源码:
`git clone git://git.openwrt.org/openwrt/openwrt.git`

4. 添加软件扩展包：
`cd /home/ngmi/openwrt/openwrt/`
`cp feeds.conf.default feeds.conf`(将feeds.conf.default修改为feeds.conf)

5. 更新扩展，安装扩展：
`./scripts/feeds update -a`
`./scripts/feeds install -a`

6. 测试下编译环境，使用命令：
   `make defconfig`

7. `make menuconfig`
   如果一切正常，会出现一个配置菜单，可以选择要编译的固件平台、型号，还能选择固件中要添加的功能和组件，至此编译环境就搭建好了。

## 在OpenWrt的路由器上安装Wifidog应用程序
1. 在OpenWrt系统的源码文件下，编辑`feeds.conf.default`文件<br>
`vim feeds.conf.default`
 在其中增加一行：
`src-git wifidog https://github.com/wifidog/wifidog-gateway.git`
2. 然后更新，再安装：
`./scripts/feeds update -a`
`./scripts/feeds install -a`
3. 终端执行编译命令
`make menuconfig`
4. 在`Network/captive portals/`下选择wifidog 就有选择 WiFiDog 这一项了

-------------------------------------------------
2019/07/09
## 配置服务器中遇到的问题
__问题：__
```ngmi@MobiNetS:~/lightFreq-master/my_wifi_auth_server$ python3 manage.py runserver 0.0.0.0:8000
Traceback (most recent call last):
  File "manage.py", line 22, in <module>
    execute_from_command_line(sys.argv)
  File "/usr/local/lib/python3.5/dist-packages/django/core/management/__init__.py", line 381, in execute_from_command_line
    utility.execute()
  File "/usr/local/lib/python3.5/dist-packages/django/core/management/__init__.py", line 375, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
  File "/usr/local/lib/python3.5/dist-packages/django/core/management/base.py", line 323, in run_from_argv
    self.execute(*args, **cmd_options)
  File "/usr/local/lib/python3.5/dist-packages/django/core/management/commands/runserver.py", line 60, in execute
    super().execute(*args, **options)
  File "/usr/local/lib/python3.5/dist-packages/django/core/management/base.py", line 364, in execute
    output = self.handle(*args, **options)
  File "/home/ngmi/lightFreq-master/my_wifi_auth_server/django_pdb/management/commands/runserver.py", line 59, in handle
    and middleware not in settings.MIDDLEWARE_CLASSES):
  File "/usr/local/lib/python3.5/dist-packages/django/conf/__init__.py", line 80, in __getattr__
    val = getattr(self._wrapped, name)
AttributeError: 'Settings' object has no attribute 'MIDDLEWARE_CLASSES'
```
__解决方法：__
```python
middleware = 'django_pdb.middleware.PdbMiddleware'
        if ((pdb_option or settings.DEBUG)
            and middleware not in settings.MIDDLEWARE):
            settings.MIDDLEWARE += (middleware,)
```
由于Django版本的问题，需要将（lightFreq-master/my_wifi_auth_server/django_pdb/management/conmands/runserver.py）以上代码中原本的MIDDLEWARE_CLASSES改为MIDDLEWARE。

__问题：__
```Invalid HTTP_HOST header: '0.0.0.0:8000'. You may need to add '0.0.0.0' to ALLOWED_HOSTS.
Bad Request: /
[09/Jul/2019 14:14:40] "GET / HTTP/1.1" 400 66219
```
进入http://0.0.0.0:8000页面显示Invalid...<br>
__解决办法：__
将lightFreq-master/my_wifi_auth_server/Auth_server/settings.py文件中`ALLOWED_HOSTS = ['192.168.1.162','127.0.0.1']`修改为`ALLOWED_HOSTS = ['*']`