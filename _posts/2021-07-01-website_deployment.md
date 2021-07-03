---
title: "在一台主机上部署多个网站" 
categories: ['后端']
tags: ['backend', 'nginx', 'tomcat', 'htpp']
resource_path: /blog/assets/2021/07/01
---

# 在一台主机上部署多个网站

- [在一台主机上部署多个网站](#在一台主机上部署多个网站)
  - [背景](#背景)
  - [网站访问过程](#网站访问过程)
  - [服务器如何区分对不同网站的请求](#服务器如何区分对不同网站的请求)
  - [Nginx ，Apache 与 Tomcat](#nginx-apache-与-tomcat)
    - [Nginx/Apache VS Tomcat](#nginxapache-vs-tomcat)
    - [Nginx VS Apache](#nginx-vs-apache)
  - [实际操作](#实际操作)
    - [方案一: 只用Tomcat](#方案一-只用tomcat)
    - [方案二: Nginx + Tomcat](#方案二-nginx--tomcat)
      - [让第一个tomcat上的网站跑起来](#让第一个tomcat上的网站跑起来)
      - [让第二个静态网站跑起来](#让第二个静态网站跑起来)
  - [最后。。。](#最后)

---

## 背景

有两个网站，一个部署在云服务器(Ubuntu 16)上，另一个托管在vercel上。大概在今年年初这段时间？vercel被墙了。。。为了让第二个网站也可以在国内访问，决定把第二个网站也部署到云服务器上。

## 网站访问过程

在讲具体如何实现之前，先讲一下相关基础知识。用户要通过浏览器访问网站主页，网页的请求过程大致分为以下几个阶段:

1. 用户在浏览器地址栏输入URL。如: `abc.com/index.html`。一般默认使用http协议，http协议默认端口是80端口；chrome正在准备将默认协议改为https，https协议默认端口是443端口。当`abc.com`访问不到时，chrome还会自动去尝试访问`www.abc.com/index.html`。一般服务端会配置首页，可以省略`/index.html`。
2. 浏览器对URL进行解析得到域名。此处域名就是`abc.com`。
3. 浏览器通过域名解析得到服务器的IP地址。一个域名最终会指向一个IP。如: `11.11.11.11`。
4. 浏览器发送http报文，向IP地址的80端口（http协议），或是443端口（https协议），发起对目标网页的请求。`11.11.11.11:80/index.html`。
5. **服务端收到请求后，对请求进行处理，将相应html页面发回给浏览器**。服务端根据配置来决定返回什么页面。由于我们之前没有指定页面，
6. 浏览器解析收到的html文件，请求后续资源等，将它渲染到浏览器中展现给用户。


## 服务器如何区分对不同网站的请求

部署多个网站，问题就出在第4、5步: 一台服务器只有一个IP地址，两个网站的域名都映射到了同一个IP上，服务端如何确定用户是请求的哪一个网站的页面呢？

通过端口吗？这不太好。 HTTP协议和HTTPS定义了浏览器(万维网客户进程)怎样向万维网服务器请求万维网文档，以及服务器怎样把文档传送给浏览器，所以一般浏览器对服务器的网页的访问都是通过http协议的默认80端口或是https协议的443端口。由于云服务器默认只开放那几个端口（http：90， https:44，ssh：22），一般不能直接通过8080访问。如果非要通过8080端口访问也可以在阿里云防火墙里配置，但是这样用户除了域名外还需要额外记个端口号，每次访问网站还要自己输端口号，很不方便。

最好能够直接根据域名来判断，答案就藏在http协议里。http请求报文的请求头部里有个一字段为[*host*](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Host)，它的值为`域名或ip地址:端口号`，端口号省略时则根据传输协议来使用对应默认端口号（http: 80，https: 443）。举个例子，如果`a.com`和`b.com`两个域名绑定到了同一台服务器上，那么当你分别通过这两个域名向服务器发出http请求时，报文的host字段就分别为`a.com`和`b.com`。这样一来，服务器就可以根据host字段来将对不同网站的请求区分开了，一般大家也都是这么做的。

## Nginx ，Apache 与 Tomcat

简单对比下三个常用的 web server：Nginx，Apache（Apache HTTPD Server） 与 Tomcat（Apache Tomcat）。

### Nginx/Apache VS Tomcat 

首先，Nginx和Apache是一类，Tomcat是一类。Nginx和Apache都是http server，关心的是 HTTP 协议层面的传输和访问控制。而Tomcat是application server，本质是一个servelet应用的容器，是apache之上的扩展，可以运行java，可以连接数据库，可以动态生成网页。前两个虽然有局限性，但也有很多优点，比如：对静态网站的访问速度更快，稳定性更好（长时间不用重新启动），安全性更高，对套接字处理的更好等[#](https://wiki.apache.org/tomcat/FAQ/Connectors#Q3)。**一般用前两个做静态网站部署、反向代理和反向代理之上的负载均衡等，用tomcat做java web应用部署。**

Nginx和Apache也各有各的优势[#](https://www.zhihu.com/question/19571087/answer/133244938)。核心区别在于，apache是同步多进程模型，一个连接对应一个进程；nginx是异步的，多个连接（万级别）可以对应一个进程。

### Nginx VS Apache

Nginx相对于Apache的优点：

1. 轻量级，同样起web 服务，比apache 占用更少的内存及资源 
2. 抗并发，nginx 处理请求是异步非阻塞的，而apache 则是阻塞型的，在高并发下nginx 能保持低资源低消耗高性能 
3. 高度模块化的设计，编写模块相对简单 
4. 社区活跃，各种高性能模块出品迅速


Apache 相对于Nginx 的优点： 

1. rewrite ，比nginx 的rewrite 强大 
2. 模块多，基本想到的都可以找到 
3. 少bug ，nginx 的bug 相对较多 
4. 超稳定



## 实际操作

两个网站，第一个是在云服务器上用tomcat搭的，第二个在vercel上。**在此基础上**（尽量不动第一个网站），要将第二个网站也部署到云服务器上，现在有几个思路: 

1. 只使用现有的tomcat服务器。在tomcat的Engine里多配置一个Host。
2. 除了现有的tomcat服务器外，再加一个nginx服务器。用nginx同时做[反向代理](https://www.zhihu.com/answer/128105528)和静态网站部署。
3. 其他。也可以将二者都放到各自的服务器上，再通过nginx反向代理到两个服务器。等等其他方案。

最后是采用了方案二。

### 方案一: 只用Tomcat

Tomcat的配置整体结构介绍可以看下文末的“[详解Tomcat 配置文件server.xml](https://www.cnblogs.com/kismetv/p/7228274.html)”这篇文章，文章讲的很好这里就不赘述了。

多个应用可以直接作为Context在同一个Host下配置。但这样还是通过同一个域名访问，不同应用通过路径来区分。

`<Context psth="浏览器要访问的目录地址" docBase="网站所在磁盘目录"/>`

要想每个域名对应到自己的网站，我们需要配置更上一级的Host(一个Host相当于一个虚拟主机):

![tomcat_multi_host]({{page.resource_path}}/tomcat_multi_host.png)

然后把静态网站的文件夹放到指定位置即可。

但是这样做有两个缺点，一是两个网站任意一个网站想要停止，另一个也必须停止。另一个是tomcat是java servlet容器，适合处理动态页面，用来处理静态网站性能比起nginx和apache httpd要差。

### 方案二: Nginx + Tomcat

大概思路是，做个反向代理。让ngnix来监听80端口，然后根据host转发给tomcat或是从第二个静态网站的文件夹里读取文件并返回。

要安装Nginx，需要保证80端口不被tomcat占用(虽然理论上多个进程可以监听同一个端口。可通过`lsof -i:80`命令查一下端口占用情况)，不然会安装失败[#]()。我这里需要先停下之前占用80端口的tomcat。在这里遇到一个坑，`service tomcat stop` 失败，用`systemctl status tomcat`检查了一下，发现`.../tomcat/bin/shutdown.sh`执行失败。`shotdown.sh`里面实际上是通过执行同目录下的`sh catalina.sh stop`来启动服务器的，直接执行后一个命令发现可以正常关闭了。并对应的`/etc/systemd/system`目录下的`tomcat.service`对应位置改了下。

tomcat停了之后，就可以正常用`sudo apt-get install nginx`来安装nginx了。当然最好在安装前用`sudo apt-get update`更新一下索引。执行完命令之后，`/etc/systemd/system`下就出现了`nginx.service`这个文件，我们就可以正常通过`service nginx start`来启动nginx了。

接下来需要对tomcat和ngnix的配置文件进行修改。这里分两大步，先让第一个tomcat上的网站通过nginx重新跑起来，再让第二个静态网站跑起来。

#### 让第一个tomcat上的网站跑起来

我们要让nginx来监听80端口，就要让tomcat放弃80端口而转用其他端口。tomcat配置文件的相对路径是`tomcat/conf/server.xml`。在配置文件中定位到port为80的Connector, 将他监听的端口改为其他未被使用的端口(注意使用1024-65535的动态端口号)，我这里改为了tomcat默认端口号8080。

接下来让nginx来监听80端口，转发host为第一个网站域名的数据转发给tomcat的8080端口。nginx配置文件的绝对路径是`/etc/nginx/nginx.conf`。打开配置文件，找到http这个key，我们需要在它的里面添加server:

![nginx_config1]({{page.resource_path}}/nginx_config1.png)

这里带‘ssl’字眼的那五行可以先忽略，那是在配置https相关的东西。关键在于：

1. `listen 80`：监听80端口
2. `server_name www.abc.net`：这里填之前说的host，即域名。
3. `proxy_set_header Host $host`：设置Host字段为原始请求的Host。
4. `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for`：设置X Forwarded For字段为客户端的原始IP。
5. `proxy_pass localhost:8080`：转发到本地8080端口。

现在，启动nginx和tomcat，会发现第一个网站已经可以被正常访问了，接下来我们需要让第二个网站也跑起来。

#### 让第二个静态网站跑起来

让第二个静态网站跑起来就比较容易了。之前第二个静态网站是挂在vercel上，当github上有新的push时，项目会进行部署。

为了简单起见，这里先不考虑自动化部署，先手动在本地对项目build，然后把build生成的整个文件夹通过winscp/xftp等工具上传到服务器上。

以后，对nginx进行简单配置即可。在之前的server下再配置一个新的server。与第一个网站的server相比，这里不需要对请求进行转发，而是需要直接指定项目的根目录。

![nginx_config1]({{page.resource_path}}/nginx_config1.png)

除了`listen`和`server_name`外，还有两个关键配置：

1. `root /opt/public`：将域名后面的路径`/`映射到本地的`/opt/public`。如：`abc.com/test/test.html`，会访问本地的`/opt/public/test/test.html`。
2. `index`：默认首页

这样一来，tomcat上的网站想要停止时，不会影响到nginx上部署的其他静态网站。并且比起只用tomcat，第二个静态网站的访问速度也有所提升。

## 最后。。。

至此，在已经有一个基于tomact的动态网站的基础上，第二个静态网站就部署好了。


---

相关资料

- 王道考研计算机网络
- [Host - MDN](#https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Host)
- [正向代理与反向代理](https://www.zhihu.com/answer/128105528)
- [详解Tomcat 配置文件server.xml](https://www.cnblogs.com/kismetv/p/7228274.html)
- [详解tomcat的连接数与线程池](https://www.cnblogs.com/kismetv/p/7806063.html)
- [tomcat中server.xml配置文件中几个port的作用和区别](https://blog.csdn.net/zhuzhezhuzhe1/article/details/80431015)
- [hosts](https://baike.baidu.com/item/hosts/10474546?fromtitle=Hosts%E6%96%87%E4%BB%B6&fromid=8971674)
- [Tomcat运行脚本及启动过程分析--catalina.sh](https://blog.csdn.net/qq_21508059/article/details/82713797)
- [Apache Tomcat Configuration Reference](http://tomcat.apache.org/tomcat-5.5-doc/config/host.html)
- [Tomcat和Apache HTTPD的关系](https://blog.csdn.net/xinyuanqianxun1987/article/details/89202129)
- [Apache和Apache Tomcat的区别是什么？](https://www.zhihu.com/question/37155807/answer/72706896)
- [从运行原理及使用场景看Apache和Nginx](https://blog.csdn.net/pkgray/article/details/43411589)
- [tomcat部署静态网站的五种方法](https://blog.csdn.net/w893932747/article/details/75043411)
- [Configuration File’s Structure - Nginx](http://nginx.org/en/docs/beginners_guide.html#conf_structure)
- [githubActions部署文件到服务器](https://blog.csdn.net/qq_39846820/article/details/115422544)
- [nginx配置https和http共存](https://blog.csdn.net/qq_39403545/article/details/86545775)
