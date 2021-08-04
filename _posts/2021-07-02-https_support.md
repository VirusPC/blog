---
title: "给网站添加 HTTPS 支持" 
categories: ['后端']
tags: ['backend', 'nginx', 'tomcat', 'http']
resource_path: /blog/assets/2021/07/02
---

# 给网站添加 HTTPS 支持

- [给网站添加 HTTPS 支持](#给网站添加-https-支持)
  - [背景](#背景)
  - [HTTPS 与 SSL 证书](#https-与-ssl-证书)
  - [使用方法](#使用方法)
    - [下载工具](#下载工具)
    - [申请证书](#申请证书)
    - [修改服务器配置](#修改服务器配置)
    - [重启nginx](#重启nginx)

---

## 背景

在[上篇文章中](https://viruspc.github.io/blog//%E5%90%8E%E7%AB%AF/2021/07/01/website_deployment.html), 我们成功将网站部署到了云服务器上。

但是我们只能通过 http 协议来访问网站，这就给用户带来很大不便：比如对于 chrome， 用户直接输入不带协议的网站链接时，会默认加 https 协议，用户要想访问网站必须手动在网站最前面加 http 协议。

此外，HTTP是全裸式的明文请求，域名、路径和参数都被中间人们看得一清二楚，可以被劫持和篡改网页内容。HTTPS做的就是给请求加密，让其对用户更加安全。在没有HTTPS时，运营商可在用户发起请求时直接跳转到某个广告，或者直接改变搜索结果插入自家的广告。浏览器和安全软件可以监听用户搜索在结果页植入广告。一些中间人还可把用户数据直接转卖，这样你搜索了“保险”以后你就成了保险公司电话推销的目标。如果劫持代码出现了BUG，则直接让用户无法搜索，出现白屏。

于是，决定给网站加上 https 协议。

## HTTPS 与 SSL 证书

HTTPS 主要由两部分组成：HTTP + SSL/TLS。其中SSL（Security Socket Layer）是一种广泛运用在互联网上的资料加密协议；TLS是SSL的下一代协议。

HTTPS核心的一个部分是数据传输之前的握手，握手过程中确定了数据加密的密码。在握手过程中，网站会向浏览器发送SSL证书，SSL证书和我们日常用的身份证类似，是一个支持HTTPS网站的身份证明，SSL证书里面包含了网站的域名，证书有效期，证书的颁发机构以及用于加密传输密码的公钥等信息，由于公钥加密的密码只能被在申请证书时生成的私钥解密，因此浏览器在生成密码之前需要先核对当前访问的域名与证书上绑定的域名是否一致，同时还要对证书的颁发机构进行验证，如果验证失败浏览器会给出证书错误的提示。

大多数SSL证书都需要钱，功能越强大的证书费用越高。阿里云每年有固定数量的免费SSL证书提供，有效期1年。 *Lets' Encrypt* 是目前适用范围最为广泛的SSL证书，唯一的缺陷是，它发行的证书有效期只有3个月。

*Let's Encrypt*由互联网安全研究小组（缩写ISRG）提供服务。主要赞助商包括电子前哨基金会、Mozilla基金会、Akamai以及思科。2015年4月9日，ISRG与Linux基金会宣布合作。用以实现新的数字证书认证机构的协议被称为自动证书管理环境（ACME）。

而我们要用的 [acme.sh](https://github.com/acmesh-official/acme.sh) 是纯用shell语言写的，实现了acme 协议，可以从 *Let's Encrypt* 自动生成、续订和安装证书。

## 使用方法

注意云服务器要在安全组策略/防火墙里开放443端口。

### 下载工具  

```bash
curl https://get.acme.sh | sh
```

### 申请证书

一般使用两种方式验证：http 或 dns 验证。

1. http验证。`-d`后跟的是网站地址，`-w`后跟的是网站工作目录的路径。  

    ```bash
    acme.sh --issue -d www.abc.net -d abc.net -w /opt/tomcat/apache-tomcat-8.5.42/webapps/ROOT/
    ```
2. dns验证。注意为了安全最好在阿里云开个子账户用于操作，并给予DNS域名管理的权限。

    ```bash
    见官网
    ```
    
申请成功后，最后控制台会打印一些信息, 用于对服务器机型配置。

```bash
[Wed Jun 30 22:10:13 CST 2021] Your cert is in  /root/.acme.sh/www.abc.net/www.yunhaiwang.net.cer 
[Wed Jun 30 22:10:13 CST 2021] Your cert key is in  /root/.acme.sh/www.abc.net/www.abc.net.key 
[Wed Jun 30 22:10:13 CST 2021] The intermediate CA cert is in  /root/.acme.sh/www.abc.net/ca.cer 
[Wed Jun 30 22:10:13 CST 2021] And the full chain certs is there:  /root/.acme.sh/www.abc.net/fullchain.cer
```

通过`crontab -l`，可以看到添加了定时任务，用于自动更新证书。

![crontab]({{page.resource_path}}/crontab.png)

### 修改服务器配置

1. 添加`443 ssl`的监听。旧版nginx的配置是直接监听`443`端口，然后加一行`ssl true`。
2. 配置ssl_certificate。使用上面的最后一个`fullcahin.cer`即可。
3. 配置ssl_certificate_key。上面的第二行。

![nginx_https]({{page.resource_path}}/nginx_https.png)

### 重启nginx

`nginx -s reload` 或 `service nginx restart` 或 `systemctl restart nginx`


---

相关资料

- 王道考研计算机网络
- [nginx配置https和http共存](https://blog.csdn.net/qq_39403545/article/details/86545775)
- [HTTPS 与 SSL 证书概要](https://www.runoob.com/w3cnote/https-ssl-intro.html)
- [HTTPS之acme.sh申请证书](https://www.cnblogs.com/clsn/p/10040334.html)
- [acme.sh](https://github.com/acmesh-official/acme.sh)
- [为什么需要HTTPS？全网HTTPS还有多远？](https://zhuanlan.zhihu.com/p/19979483)