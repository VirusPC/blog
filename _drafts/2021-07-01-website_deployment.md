# 在一台服务器上部署多个网站

- [背景](#背景)
- [网站访问过程](#网站访问过程)

---

## 背景

有两个网站，一个部署在云服务器(Ubuntu 16)上，另一个托管在vercel上。大概在今年年初这段时间？vercel被墙了。。。为了让第二个网站也可以在国内访问，决定把第二个网站也部署到云服务器上。

## 网站访问过程

在讲具体如何实现之前，先讲一下相关基础知识。

用户要通过浏览器访问网站主页，网页的请求过程大致分为以下几个阶段:

1. 用户在浏览器地址栏输入URL。如: abc.com。
2. 浏览器对URL进行解析得到域名
3. DNS对域名进行解析得到服务器的IP地址并将其返回给浏览器(具体流程见其他博客)。如: 11.11.11.11
4. 浏览器通过http协议向IP地址的80端口发起网页请求(在输入网站的时候其实浏览器(除了IE)已经帮你输入协议了。chrome之前默认协议是http协议，有向https发展的趋势。http协议的默认端口是80端口)。如: 11.11.11.11:80
5. 服务端收到请求后将html页面发回浏览器
6. 浏览器解析html文件，请求后续资源等，将它渲染到浏览器中展现给用户

## 服务器如何区分对不同网站的请求

部署多个网站，问题就出在第3步和第4步之间: 一台服务器只有一个IP地址，两个网站的域名都映射到了同一个IP上，服务端如何确定用户是请求的哪一个网站的页面呢？

通过端口吗？这是不可行的。 HTTP协议定义了浏览器(万维网客户进程)怎样向万维网服务器请求万维网文档，以及服务器怎样把文档传送给浏览器，所以对服务器的访问必须要经过http协议的默认端口号80。我们平常在本地通过localhost:8080可以访问是因为？

答案就藏在http协议里。http请求报文的请求头部里有个一字段为host,  它的值为域名或ip地址:端口号。举个例子，如果a.com和b.com两个域名绑定到了同一台服务器上，那么当你分别通过这两个域名向服务器发出http请求时，报文的host字段就分别为a.com和b.com。这样一来，服务器就可以根据host字段来将对不同网站的请求区分开了。

## 实际操作

两个网站，第一个是在云服务器上用tomcat搭的，第二个在vercel上。要将第二个网站也部署到云服务器上，现在有两个思路: 1. 在tomcat的Engine里多配置一个Host 2. 用nginx做个[反向代理](https://www.zhihu.com/answer/128105528)。最后是采用了方案二。

### 方案一: 只用Tomcat

Tomcat的配置整体结构介绍可以看下文末的`详解Tomcat 配置文件server.xml`这篇文章，文章讲的很好这里就不赘述了。

多个应用可以直接作为Context在同一个Host下配置。但这样还是通过同一个域名访问，不同应用通过路径来区分。

要想每个域名对应到自己的网站，我们需要配置更上一级的Host(一个Host相当于一个虚拟主机):

`<Context psth="浏览器要访问的目录地址" docBase="网站所在磁盘目录"/>`

然后把静态网站的文件夹放到指定位置即可。

但是这样做有两个缺点，一是两个网站任意一个网站想要停止，另一个也必须停止。另一个是tomcat是java servlet容器，适合处理动态页面，用来处理静态网站性能比起nginx和apache httpd要差。所以最后是使用了方案二 ( 其实主要是因为想玩玩nginx :) )。

### 方案二: Nginx + Tomcat

大概思路是，做个反向代理。让ngnix来监听80端口，然后根据host转发给tomcat或是从第二个静态网站的文件夹里读取文件并返回。

要安装Nginx，需要保证80端口不被占用(可通过`lsof -i:80`命令查一下端口占用情况)，不然会安装失败[#]()。我这里需要先停下之前占用80端口的tomcat。在这里遇到一个坑，`service tomcat stop` 失败，用`systemctl status tomcat`检查了一下，发现`.../tomcat/bin/shutdown.sh`执行失败。`shotdown.sh`里面实际上是通过执行同目录下的`sh catalina.sh stop`来启动服务器的，直接执行后一个命令发现可以正常关闭了。并对应的`/etc/systemd/system`目录下的`tomcat.service`对应位置改了下。

tomcat停了之后，就可以正常用`sudo apt-get install nginx`来安装nginx了。当然最好在安装前用`sudo apt-get update`更新一下索引。执行完命令之后，`/etc/systemd/system`下就出现了`nginx.service`这个文件，我们就可以正常通过`service nginx start`来启动nginx了。

接下来需要对tomcat和ngnix的配置文件进行修改。这里分两大步，先让第一个tomcat上的网站通过nginx重新跑起来，再让第二个静态网站跑起来。

#### 让第一个tomcat上的网站跑起来

我们要让nginx来监听80端口，就要让tomcat放弃80端口而转用其他端口。tomcat配置文件的相对路径是`tomcat/conf/server.xml`。在配置文件中定位到port为80的Connector, 将他监听的端口改为其他未被使用的端口(注意使用1024-65535的动态端口号)，我这里改为了tomcat默认端口号8080。

接下来让nginx来监听80端口，转发host为第一个网站域名的数据转发给tomcat。nginx配置文件的绝对路径是`/etc/nginx/nginx.conf`。打开配置文件，找到http这个key，我们需要在它的里面添加server:

现在，启动nginx和tomcat，会发现第一个网站已经可以被正常访问了，接下来我们需要让第二个网站也跑起来。

#### 让第二个静态网站跑起来

让第二个静态网站跑起来就比较容易了。之前第二个静态网站是挂在vercel上，当github上有新的push时，项目会进行部署。

为了简单起见，这里先不考虑自动化部署，先手动在本地对项目build，然后把build生成的整个文件夹通过winscp/xftp等工具上传到服务器上。

以后，对nginx进行简单配置即可。在之前的server下再配置一个新的server。与第一个网站的server相比，这里不需要对请求进行转发，而是需要直接指定项目的根目录。这里只列出server的关键配置。



这样一来，tomcat上的网站想要停止时，不会影响到nginx上部署的其他静态网站。并且比起只用tomcat，第二个静态网站的访问速度也有所提升。

### 其他方案
除了上面两个方法，也可以在服务器上再开第二个nginx或apache来跑第二个静态网站，然后用第一个nginx来转发请求给它或是tomcat等。这样做的好处是任意一个网站想要停止都不会影响其他网站。但是两个网站的访问频率不是很高，需要关一个开一个的情景也比较少，多跑一个服务器要多占些内存, 也懒得再搞了。

## 最后。。。
至此，一个动态网站，一个静态网站就部署好了。由于https协议需要ssl证书, ssl证书需要花钱, 所以就没有对443端口进行配置. 如果要配置的话, 注意在端口号后加上ssl这个单词: `listen 443 ssl`.


---

相关资料

- 王道考研计算机网络
- [正向代理与反向代理](https://www.zhihu.com/answer/128105528)
- [详解Tomcat 配置文件server.xml](https://www.cnblogs.com/kismetv/p/7228274.html)
- [详解tomcat的连接数与线程池](https://www.cnblogs.com/kismetv/p/7806063.html)
- [tomcat中server.xml配置文件中几个port的作用和区别](https://blog.csdn.net/zhuzhezhuzhe1/article/details/80431015)
- [hosts](https://baike.baidu.com/item/hosts/10474546?fromtitle=Hosts%E6%96%87%E4%BB%B6&fromid=8971674)
- [Tomcat运行脚本及启动过程分析--catalina.sh](https://blog.csdn.net/qq_21508059/article/details/82713797)
- [Apache Tomcat Configuration Reference](http://tomcat.apache.org/tomcat-5.5-doc/config/host.html)
- [Tomcat和Apache HTTPD的关系](https://blog.csdn.net/xinyuanqianxun1987/article/details/89202129)
- [tomcat部署静态网站的五种方法](https://blog.csdn.net/w893932747/article/details/75043411)
- [Configuration File’s Structure - Nginx](http://nginx.org/en/docs/beginners_guide.html#conf_structure)
- [githubActions部署文件到服务器](https://blog.csdn.net/qq_39846820/article/details/115422544)
- [nginx配置https和http共存](https://blog.csdn.net/qq_39403545/article/details/86545775)
