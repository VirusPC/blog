---
title: "用 Github Actions 做持续部署" 
categories: ['后端']
tags: ['backend', 'nginx', 'github', 'http', 'CI/CD']
resource_path: /blog/assets/2021/08/04
---

{% include posts_hook.html %}

# 用 Github Actions 做持续部署

- [用 Github Actions 做持续部署](#用-github-actions-做持续部署)
  - [背景](#背景)
  - [CI/CD](#cicd)
  - [静态网站托管](#静态网站托管)
  - [Github Actions](#github-actions)
    - [Workflows](#workflows)
    - [Events](#events)
    - [Jobs](#jobs)
    - [Steps](#steps)
    - [Actions](#actions)
    - [Runners](#runners)
  - [Tmux](#tmux)
  - [配置流程](#配置流程)
    - [第一步, 创建新用户](#第一步-创建新用户)
    - [第二步, 克隆项目到服务器, 并项目相关环境](#第二步-克隆项目到服务器-并项目相关环境)
    - [第三步, 添加 self-hosted runner](#第三步-添加-self-hosted-runner)
    - [第四步, 编写 workflow](#第四步-编写-workflow)
  - [网站预览](#网站预览)

---

## 背景

在[之前的文章中](https://viruspc.github.io/blog//%E5%90%8E%E7%AB%AF/2021/07/01/website_deployment.html), 我们成功将静态网站部署到了云服务器上。具体方法是先将静态网站在本地 build 之后, 将 build 之后的整个文件夹上传到服务器, 然后 nginx 会根据用户输入的 url 来返回网页[*]([](https://viruspc.github.io/blog//%E5%90%8E%E7%AB%AF/2021/07/01/website_deployment.html#%E8%AE%A9%E7%AC%AC%E4%BA%8C%E4%B8%AA%E9%9D%99%E6%80%81%E7%BD%91%E7%AB%99%E8%B7%91%E8%B5%B7%E6%9D%A5)
). 

但是每次网站更新都要先 build 再上传就很麻烦耗时且容易出错，于是考虑做自动化部署。本文是使用了 github 提供的 CI/CD 服务：[github actions](https://github.com/features/actions) 来做持续部署。

---

## CI/CD

CI/CD 的解释可以参考 [Red Hat 的文章](https://www.redhat.com/zh/topics/devops/what-is-ci-cd)和[阮一峰老师的文章](https://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)。 简单来说，CI全称为Continuous Integration，意为持续集成，是在源代码变更后自动检测、拉取、构建和进行自动化测试的过程，属于开发人员的自动化流程。CD 指的是持续交付 (Continuous Delivery) 或持续部署 (Continuous Deployment)。持续交付通常是指开发人员对应用的更改会自动进行错误测试并上传到存储库（如 GitHub 或容器注册表), 然后由运维团队将其部署到实时生产环境中. 持续部署指的是自动将开发人员的更改从存储库发布到生产环境，它以持续交付为基础，实现了管道后续阶段的自动化。

![CI/CD]({{page.resource_path}}/cicd.png)

本文仅讨论持续部署方面的东西。

---

## 静态网站托管

虽然本文的目标网站是放在自己的服务器上，并没有托管，需要的只 是CI/CD 工具，但在这里还是随便写一些静态网站托管相关的东西。

并不是所有的网站都需要持续部署. 对于那些在使用期间内容永远不变的网站，我们只需简单的将网页挂到网上即可。对于这类网站，我们可以借助于各云服务商的[**对象存储**](https://zhuanlan.zhihu.com/p/166289089)服务，或是**云函数服务**或是直接的**页面托管服务**等。

通过对象存储来托管静态网站可参考 [腾讯云COS 静态网站托管](https://cloud.tencent.com/document/product/436/9512)，[阿里云OSS 静态网站托管](https://www.alibabacloud.com/help/zh/doc-detail/31872.htm?spm=a2c63.p38356.b99.171.38ce20cdPhz8hm)等官方的文档。云函数服务 [vercel](vercel.com)，[阿里云](https://www.aliyun.com/product/fc)，[腾讯云](https://cloud.tencent.com/document/product/583/32996)，[轻服务（字节跳动）](https://qingfuwu.cn/docs/nodejs/cloud-function/basic.html#runtime)等公司也都有提供。用云函数来做网站托管的话，其实就是给你提供个云服务器，你可以上传一些文件，让你在上面跑个 nodejs 之类的后端，需要手写根据url请求来返回页面。吐槽一下腾讯云的 Nodejs 版本很低... 写本文的时候最新版本是16.4, 它提供的版本是12.16, 对比vercel上云函数的版本是当前nodejs的推荐版本14.x。不过如果你的网站放在腾讯云云服务器上的话, 它有个专门 CI 的选项比较方便。利用云函数来托管网站并不方便，因为我们还需要手写服务端逻辑之类的，所以像vercel，轻服务等也提供了直接的页面托管服务，直接把 build 后的页面上传即可。注意如果使用云函数的话，不同厂商支持的语言种类不同，比如字节的轻服务的云函数只支持 nodejs，不支持 python 等。

![tencent cloud]({{page.resource_path}}/tencentcloud.png)

但是，有些网站我们希望可以与仓库绑定，当仓库里的资源更新时，网站也会自动更新。上面说的 vercel 可以与 github 仓库关联起来，使展示的页面与仓库同步。并且，我们可以不先对前端项目进行打包，而是将这一过程交给 vercel 自动进行。此外，vercel 还帮我们做了 CDN （内容分发网络），感觉最大的缺点就是年初的时候被墙了。。。github 官方的 github pages 也可以左到与仓库同步，但同样的需要先打包再部署（除非你像本博客网站一样用的 jekyll 框架）。[这里](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages#usage-limits)写着它的项目大小,带宽等局限性. 我们想要更高级的功能, 可以借助于后面说的[github actions](#github-actions). 毕竟, 就像官方说的:

> GitHub Pages **is not intended for or allowed to be used as a free web hosting service** to run your online business, e-commerce site, or any other website that is primarily directed at either facilitating commercial transactions or providing commercial software as a service (SaaS)


---

## Github Actions

CI/CD 工具有很多。GitHub Actions 是 GitHub 官方推出的 CI/CD 服务, 可以很方便的与 github 项目结合使用. 它可以帮助你在软件开发生命周期中自动执行指定的任务. 它是**事件驱动**的, 比如, 当发生 push 事件时, 自动执行一些软件测试命令.

所以, 使用 github actions, 我们需要指定要执行的任务(workflow), 以及任务的触发条件(event).

![]({{page.resource_path}}/github-actions.png)

![]({{page.resource_path}}/github-actions2.png)

### Workflows

一个 workflow 是一个自动化过程. 由很多 [job](#jobs) 组成, 可被 [event](events) 触发. 一个 workflow 写在一个 yaml 文件中, 这个文件必须放置在 `/.github/workflows` 目录中. 可以放置很多个 workflow. 

### Events

可以触发 [workflow](#workflows) 的 event 有很多. 常用的有 push, pull_request, release 等. 参考[本文](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

### Jobs

一个 workflow 可以有多个 job. 每个 job 都在自己的 [runner](#runners) 中执行, 多个 job 并行执行 (也可以配置成串行).

### Steps

一个 [job](#jobs) 由多个 step 组成. 一个 step 可以是一个 [action](#actions) 或是 shell command. 同一个 job 的 step 会在同一个 [runner](#runners) 中按顺序执行.

### Actions

action 是独立的命令, 作为一个 step 来使用. 你可以创建自己的 action, 也可以使用社区中的 action.

### Runners

一个 runner 是一个安装有 [GitHub Actions runner application](https://github.com/actions/runner) 的**服务器**. 可以使用 github 提供的 runner, 也可以使用自己部署的 runner. job 内的 commands 会在 runner 中执行.

---

## Tmux

[Tmux](https://github.com/tmux/tmux/wiki/Getting-Started) 是一个终端多路复用器. 它使得用户可以在一个终端中再开启多个终端 (session).

在后面部署 github actions 的 runner 时, 会出现一种情况: runner 在 run 之后会一直占用终端使得你无法进行输入其他命令 (就像开启一个 server 一样). 而且, 当你断开与云服务器的连接后, 这个终端也会被关闭, runner 也随着被关闭, 无法执行 workflow. 我们可以通过 tmux 另外开启个终端来解决这一问题.

几个关键命令如下:

1. 创建新的 session: 
   1. `tmux new -s <session_name>`
   2. 先按`ctrl` + `b`, 再按 `c`
2. 暂时退出当前 session: 
   1. `tmux detach`
   2. 先按 `ctrl` + `b`, 接着按 `d`
3. 进入之前的 session:
   1. `tmux a -t <session_name>`
4. 关闭 session:
   1. `tmux kill-session -t <session_name>`
5. 列出所有 session:
   1. `tmux ls`

---

## 配置流程

环境:

- ubuntu 14.0
- node 16
- yarn 4
- gatsby 2.28.2

### 第一步, 创建新用户

为确保 github actions 不能随意操作服务器上的文件, 需要创建一个新用户, 确保他拥有自动化部署所需要的最小权限.

1. 创建用户，并添加home目录.
    ```bash
    adduser -m ideaslab-website-runner
    ```

2. 切换到新建用户
    ```bash
    su ideaslab-website-runner 切换用户
    ```

3. 切换到用户根目录
    ```bash
    cd ~
    ```

### 第二步, 克隆项目到服务器, 并项目相关环境

1. clone 项目到新建用户目录下. 注意使用 ssh 模式，不然每次 push 都要输账号密码. 第一次连 gihub 的话, 需要先用`ssh-keygen -t rsa -C "your_email@xxx.com`在`~/.ssh`下生成密钥并在 gihub 进行相关配置.

2. 安装 nodejs (使用的是 16.4.2)
    ```bash
    curl -O https://nodejs.org/dist/v11.8.0/
    tar -xvf node-v16.4.2-linux-x64.tar.xz node-v16.4.2-linux-x64/
    ```

3. 配置环境变量, 让 node 命令可以全局被使用. 现在有个问题, 每次切换用户都要重新执行 source 命令, 可通过[修改shell初始化文件](https://unix.stackexchange.com/questions/21598/how-do-i-set-a-user-environment-variable-permanently-not-session)来解决.
    ```bash
    vim /etc/profile # 在文件最下面的export PATH后添加新路径PATH=xxx, xxx为nodejs解压目录下的bin目录的路径。
    source /etc/profile # 让配置环境变量生效.
    ```

4. 安装相关依赖, 测试是否能正常build

5. 修改 `nginx.conf` 中项目的根目录为 build 后的目录.

### 第三步, 添加 self-hosted runner

1. 跟着[官网流程]((https://docs.github.com/en/actions/hosting-your-own-runners/adding-self-hosted-runners#adding-a-self-hosted-runner-to-a-repository))走. 为网站的 github 项目, 添加 self-hosted runner.
    
    ![]({{page.resource_path}}/self-hosted_runner1.png)

2. 继续跟着指示走. 将 runner 放在 `~/actions-runner` 文件夹下. 但走完 Download 后, 先**不要着急执行 Configure 的步骤**.
    ![]({{page.resource_path}}/self-hosted_runner2.png)

3. 新建终端. 由于是远程连接的云服务器, 如果直接执行后续的 `run.sh` 脚本, 当你关闭终端后, runner 也就自动关闭了. Tmux 可以帮助我们再开多个终端, 而新建的终端不会随着我们与服务器连接的断开而关闭, 这样 runner 就可以一直保持运行状态. 更多使用方式可参考文末参考资料中的 tmux 使用手册.
    ```bash
    sudo apt-get install tmux  # 安装
    tmux new -s ghrunner # 新建 session
    tmux a -t ghrunner # 切换 session
    ```

4. 在新的 session 中, 切换回 actions-runner 用户, 执行剩下 Configure 的步骤. 可以看到 runner 已经在正常运行了.
    ![]({{page.resource_path}}/self-hosted_runner3.png)
    ![]({{page.resource_path}}/self-hosted_runner4.png)

5. 切换回默认终端.
   ```bash
   # 用快捷键, 先按 ctrl + b, 再按 d
   ```

### 第四步, 编写 workflow

在github项目的根目录下新建`.github/workflows/xxx.yml`, 并编写workflow.

自动化部署的 workflow 的核心步骤就是, 当项目发生 push 事件时, 服务器自动进行 pull, install 和 build. 下图是此项目的 workflow.

![workflow]({{page.resource_path}}/workflow.png)

---

## 网站预览

经过上面的步骤，在服务端，我们有了github仓库，以及一个运行着的 runner。当用户执行 git push 操作时，runner 会自动执行 workflow 里的内容：拉去远程仓库到本地，并且进行项目构建。项目构建好后，就可以访问到新的内容了。

一般来说，不应该在网站主分支上直接做改动，我们需要一个分支来对网站进行修改和预览。为了实现这个目的，在之前的基础上，我们需要：

1. 再重新 clone 一遍仓库到服务器，新的仓库将用于预览。

2. 修改 workflow，区分主分支和预览分支，为每个分支编写自己的 workflow。下图中将 master 分支作为 production 分支，release 分支作为 preview 分支。

![workflow]({{page.resource_path}}/production-workflow.png)
![workflow]({{page.resource_path}}/preview-workflow.png)

3. 配置 DNS，新增预览用的子域名，并在 nginx 里配置好，让域名指向预览仓库的 dist(build) 文件夹。



---

参考资料

- [Github Actions](https://github.com/features/actions)
- [Adding a self-hosted runner to a repository](https://docs.github.com/en/actions/hosting-your-own-runners/adding-self-hosted-runners#adding-a-self-hosted-runner-to-a-repository)
- [CI/CD是什么？](https://www.redhat.com/zh/topics/devops/what-is-ci-cd)
- [持续集成是什么？](https://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)
- [搭建自己CI/CD（一）](https://zhuanlan.zhihu.com/p/163526198)
- [ubuntu 安装 nodejs](https://zhuanlan.zhihu.com/p/55895711)
- [How do I set a user environment variable](https://unix.stackexchange.com/questions/21598/how-do-i-set-a-user-environment-variable-permanently-not-session)
- [tmux 使用手册](http://louiszhai.github.io/2017/09/30/tmux/#%E8%BF%9B%E5%85%A5%E4%B9%8B%E5%89%8D%E7%9A%84%E4%BC%9A%E8%AF%9D)
- [Ubuntu Linux系统环境变量配置文件](https://www.cnblogs.com/lovebay/p/11236255.html)
- [Linux下source命令详解](https://www.cnblogs.com/shuiche/p/9436126.html)
- [使用git提交到github,每次都要输入用户名和密码的解决方法](https://www.cnblogs.com/sky6862/p/7992736.html)
- [Vercel](https://vercel.com/docs/)
- [轻服务](qingfuwu.cn)
- [对象存储，为什么那么火](https://zhuanlan.zhihu.com/p/166289089)
- [腾讯云-SCF-静态网站托管](https://cloud.tencent.com/document/product/583/32996)
- [腾讯云-COS-静态网站托管](https://cloud.tencent.com/document/product/436/9512)
- [阿里云-OSS-静态网站托管](https://www.alibabacloud.com/help/zh/doc-detail/31872.htm?spm=a2c63.p38356.b99.171.38ce20cdPhz8hm)
- [阿里云-函数计算FC](https://www.aliyun.com/product/fc)
- [轻服务-云函数](https://qingfuwu.cn/docs/nodejs/cloud-function/basic.html#runtime)