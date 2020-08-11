---
title: 'React: 脚手架'
categories: ['前端']
tags: ['前端', 'React', 'javascript', 'scaffolding']
resource_path: /blog/assets/2020/08/11/react_scaffolding
---

React: 脚手架 
===

---

## 目录

1. [脚手架概述](#脚手架概述)
2. [创建项目并启动](#创建项目并启动)
3. [react脚手架项目结构](#react脚手架项目结构)
4. [脚手架中的组件](#脚手架中的组件)

---

## 脚手架概述

1. 什么是脚手架?  
    xxx脚手架(scaffolding)是用来帮助程序员快速创建一个基于xxx库的模板项目:  
    * 包含了所有需要的配置
    * 指定好了所有的依赖
    * 可以直接安装/编译/运行一个简单效果

2. 不用脚手架直接写的问题:
    * 结构不合理
    * 很多功能做不了

3. react脚手架的作用:
    * 自动翻译jsx
    * 自动打开浏览器
    * 自动刷新页面
    * 压缩代码
    * 检查代码
    * 扩展前缀
    * 工程化开发
    * 组件->文件夹
    * ...

4. 使用脚手架:
    * 方法一: 按照官方文档配置好
    * 方法二: 搭建压缩包
        * 在指定网络地址下载
        * 在 npm 上下载

5. 单纯的脚手架没有任何会产生效果的代码. React 不仅如此, 它提供的是基于脚手架的一个有简单效果的项目(有一个主页).

6. React 提供了一个用于创建 React 项目的脚手架库: ```react-craete-app```.

7. 项目的整体架构:  
    react + webpack +es6 + eslint

8. 使用脚手架开发的项目的特点:  
    模块化\组件化\工程化.

9. 改页面不可以HMR, 改样式可以HMR. (webpack dev-server)


## 创建项目并启动

1. 安装  
    ```bash
    npm install -g create-react-app
    ```
    
2. 创建 React 脚手架项目:  
    ```bash
    create-react-app hello-react
    ```

3. 启动项目  
    ```bash
    cd hello-react
    npm start 或 npm run start
    ```

## react脚手架项目结构

1. 项目结构(核心):  
    ReactNews  
    　　|--node_modules------第三方依赖模块文件夹  
    　　|--public  
    　　　　|--favicon.ico---网站图标  
    　　　　|--index.html----主页面  
    　　|--src---------------源码文件夹  
    　　　　|--components----放置自定义react组件的文件夹.  
    　　　　|--index.js------应用入口js  
    　　　　|--[App.js](https://www.bilibili.com/video/BV1uK411H7on?p=45)-----------App组件.  
    　　|--.gitignore--------git版本控制忽略的配置  
    　　|--package.json------应用包配置文件  
    　　|--README.md---------应用描述说明的readme文件  

2. 项目结构(其它):  
    ReactNews  
    　　|--public  
    　　　　|--css-------------成型的第三方库. 如 bootstrap.css. 其它地方通过link引用.
    　　　　|--logo192.png-----192x192. manifest.json中会用到.  
    　　　　|--logo512.png-----512x512. manifest.json中会用到.  
    　　　　|--manifest.json---生成手机应用时用到的配值.  
    　　　　|--robots.txt------爬虫相关. 告诉搜索引擎, 哪些可以爬, 哪些不可以爬. 有特殊语法.  
    　　|--src  
    　　　　|--APP.css---------APP组件相关样式  
    　　　　|--App.test.js------测试用, 编程人员一般不用  
    　　　　|--index.css--------入口文件相关样式  
    　　　　|--logo.svg--------- 组件引用的图片 
    　　　　|--serviceWorker.js-APP相关. 用于做PWA(使应用断网时不会白屏, 仍旧可以显示菜单等部分组件).  
    　　　　|--setupTests.js----测试相关, 在运行测试之前自动执行  


2. index.html 中的一些标签
    * ```<link rel="icon" href="%PUBLIC_URL%/favicon.ico" />```  
        ```%PUBLIC_URL%```代表着public文件夹的路径, **仅适用于react脚手架**. favicon.ico 页面标签的图标.
    * ``` <meta name="theme-color" content="#000000" />```
        修改安卓手机浏览器地址栏的颜色(IOS未开放相关权限)
    * ```<meta name="viewport" content="width=device-width, initial-scale=1" />```  
        屏幕适配. [参考资料](https://blog.csdn.net/Liuqz2009/article/details/89500080)
    * ```<meta name="description" content="Web site created using create-react-app"/>```  
        搜索引擎搜索时会用到.
    * ```<link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />```  
        苹果手机中, 将网页发送到主屏幕, 网页所显示的图标.
    * ```<link rel="manifest" href="%PUBLIC_URL%/manifest.json" />```  
        应用加壳生成安卓或IOS的app的时候, 需要写一些配置. 这些配值放在manifest.json中.
    * ```<noscript>You need to enable JavaScript to run this app.</noscript>```  
        当浏览器不支持JavaScript时, 显示标签内的文本.

3. App.js 相关
    * ```import logo from './logo.svg';```  
        **react脚手架中**引入图片的方式
    * ```import `./App.css`;```  
        **react脚手架中**引入图片的方式

4. index.js 相关  
    * ```React.render(<App />, document.getElementById("peiqi"));```  
        react脚手架会自动去 /public/index.html 里找DOM元素.
    * 注意引用```react-dom```前要先引用```react```
    * 此文件只渲染了App组件, 以后添加组件应该在App组件中添加. 除了设置路由的时候, 此文件基本不会动.

5. ```ReactDOM.render()```只调用一次, 在*index.js*中.


## 脚手架中的组件

1. 一个组件定义在一个以该组件名为名的js文件中, 组件与js文件一一对应. 且其样式也应放在同名css文件中.

2. 将自定义组件与其所用资源放置在以该组件名为名的文件夹中. 并将组件相关文件夹在*src/comopnent*目录下按什么组织呢? 按DOM树的话, 重复组件会带来冗余. 把组件全部扔到同级目录下, 组件多了又会显得很乱.
    假设有一个Hello组件, 则其目录结构:   
        componnets  
        　　|--Hello-----------------组件文件夹  
        　　　　|--css------------------样式文件夹  
        　　　　　　|--Hello.css-----------样式  
        　　　　|--imgs-----------------图片文件夹  
        　　　　　　|--picture.svg---------图片  
        　　　　|--json-----------------json文件夹  
        　　　　　　|--data.json-----------json文件  
        　　　　|--Hello.jsx------------组件js  

3. 组件不仅需要定义, 还需要作为模块导出, 以便其它组件引用. 一般直接采用默认导出就好: ```export default class extends React.Component{}```

4. 导入其它组件(对应默认导出的导入): 
    * 全写: ```import MyComponent2 from ./MyComponent2.jsx```
    * 简写: ```import MyComponent2 from ./MyComponent2```

5. 导入*src*下的样式文件/图片: ```import ./App.css```/```import ./logo.svg```

6. 导入*public*下的样式库: 不应在组件中导入, 而应该在*index.html*中通过```link```导入.   
    两者区别在于, 样式库中的代码可能会引用一些我们没有下载的文件, 在组件js中导入会在导入时扫描一遍, 遇到这些找不到的引用(不合理的地方)就会立刻报错; 而通过```link```导入, 只有在真正用到时才会调用并报错.

7. 为了区别普通js与组件js, 一般组件js文件后缀名为*jsx*.

8. react遍历生成的节点不支持```unmountComponentAtNode```. 只有**index.html**里的节点可以用(```root```).


