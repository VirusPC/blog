---
title: 'webpack: 常用loader与plugin'
categories: ['前端']
tags: ['javascript', '前端', 'webpack', 'loader', 'plugin']
resource_path: /blog/assets/2020/10/16/loaders_plugins
---

# webpack: 常用loader与plugin

webpack能直接加载的只有js和json, 要想加载其他类型的资源(样式文件, 图片文件等)要使用loader, 想要更多功能(打包优化, 压缩等)需要plugin.

加载资源不再与```index.html```对话, 不再通过标签引入. 而是与 entry points (入口js文件)对话, 通过```import```引入资源.

常用的loader与plugin可以在webpack官网找到相应页面, 页面中有安装配置方法等.


* [如何找到合适的plugin和loader](#如何找到合适的plugin与loader)

* [loader的使用](#loader的使用)

* [plugin的使用](#plugin的使用)

* [开发环境的基本配置](#开发环境的基本配置)
    * [打包样式资源](#打包样式资源)
    * [打包HTML资源](#打包HTML资源)
    * [打包图片资源](#打包图片资源)
    * [打包其他资源](#打包其他资源)
    * [自动编译打包运行(server)](#自动编译打包运行)

* [生产环境的基本配置](#生产环境的基本配置)
    * [提取css成单独文件](#提取css成单独文件)
    * [css兼容性处理](#css兼容性处理)
    * [压缩css](#压缩css)
    * [js兼容性处理](#js兼容性处理)
    * [js压缩](#js压缩)
    * [HTML压缩](#HTML压缩)

* [优化配置](#优化配置)
    * [js语法检查](#js语法检查)
    * [HMR](#HMR)
    * [source-map](#source-map)
    * [oneOf](#oneOf)
    * [缓存](#缓存)
    * [tree shaking](#tree-shaking)
    * [code-split](#code-split)
    * [lazy-loading](#lazy-loading)
    * [pwa](#pwa)
    * [多进程打包](#多进程打包)
    * [externals](#externals)

---



## 如何找到合适的loader与plugin

官网上列出了一些优秀的loader与plugin(以及配置方法)
* loader: https://webpack.js.org/loaders/
* plugin: https://webpack.js.org/plugins/

利用 ctrl+f 在页面中搜索



## loader的使用

1. 安装
    * ```npm install xxx-loader -D```

2. 配置(```webpack.config.js```)
    * 暴露loader
      ```js
      module.exports = {
          ...
          module: {
              rules: [
                  {
                      test: ...  // loader要处理的文件的名字, 一般用正则表达式表示
                      loader: ...  // loader
                  }
              ]
          }
      }
      ```

3. 导入文件 
    * 直接在相应js文件中导入(而非在配置文件中导入)
      ```js
      import "xxx.jpg";
      import "xxx.css";
      ...
      ```



## plugin的使用

1. 安装
    * ```npm install xxx-plugin -D```

2. 配置(```webpack.config.js```)
    * 引入插件(CommonJS规范)  
      ```js
      const MyPlugin = require("my-plugin")
      ```
    * 暴露插件  
      ```js 
      module.exports = {
          ...
          plugins: [
              new MyPlugin({
                  ...
              }),
          ]
      }
      ```


## 开发环境的基本配置

在顶层目录创建文件夹```dist```

```webpack.config.js```中的"mode"设为"development"
 
### 打包样式资源

* ```style-loader```  
    * 用于在html文档中创建一个style标签, 将样式塞进去

* ```css-loader```  
    * 将css转换为CommonJS模块

* ```less-loader```(可选)  
    * 要配合安装less使用
    * 将less编译为css, 但不生成单独的css文件, 在内存中

在入口文件中导入样式文件: 
* ```js
  import "index.css"
  ```

不可以加载样式文件中的图片链接. 要加载需要额外的loader, 详见该部分:[打包图片资源](#打包图片资源)

### 打包HTML资源

webpack不能解析html文件, 需要借助插件编译解析. 只有打包html不是用的loader而是plugin, 不走入口文件.

* ```html-webpack-plugin```
    * 注意与**html-loader**区分开, 后者用于处理html中的标签资源
    * 注意不要再html中引入任何css和js文件

### 打包图片资源

图片文件webpack不能解析, 需要借助loader编译解析. 前两个loader用于打包样式文件中的图片, 后一个loader用于打包html中的图片.


* ```file-loader```
    *  file-loader will copy files to the build folder and insert links to them where they are included.

* ```url-loader```
    * 是```file-loader```的上层封装, 使用时需配合```file-loader```使用
    * url-loader will encode entire file bytes content as base64 and insert base64-encoded content where they are included. So there is no separate file.
    * 可以设置, 使得文件图片的大小小于某个值时, 才转base64. (一般大图片, 大于8192B, 转base64会变得更大)

* ```html-loader```
    * html中的图片url-loader没法处理, 它只能处理js中引入的图片/样式中的图片, 不能处理html中img标签.
    * 该loader可以处理html中的标签资源, 不止img标签)

Url-loader vs File-loader  

* 

### 打包其他资源
    
除了js和json及上述资源外的其他资源(如字体文件), webpack也不能解析, 需要借助loader编译解析.

* ```file-loader```
    * copy files to the build folder and insert links to them where they are included.

* 其他loader
    * 如css格式的字体文件需要打包样式文件的那些loader.

### 自动编译打包运行

live reload, 自动刷新整个页面. 想要局部刷新, 见[HMR](#HMR)

* ```webpack-dev-server```
    * 修改```package.json```中的webpack配置对象(顶级目录下)
      ```js
        devServer: {
            open: true,  // 自动打开浏览器
            compress: true,  // 启动gzip压缩(数据由服务器传输到浏览器时是可以压缩的)
            port: 3000 // 端口号
        }
      ```
    * 修改```url-loader```部分配置
        * 因为构建工具以build为根目录, 不用再找build了
        * publicPath: ```'../build/images'``` --> ```publicPath: 'images'```
    * 修改```package.json```中的scripts指令
        * 配合```webpack-cli@3.x.x```: ```"start": "webpack-dev-server"```
        * 配合```webpack-cli@4.x.x```: ```"start": "webpack serve"```
        * npm 运行某些特定名称的script的时候可以省略```run```, 比如start: ```npm run start``` 等价于 ```npm start```.
    * 运行指令: ```npm run start```


## 生产环境的基本配置

1. 创建文件夹```config```, 将```webpack.config.js```复制两份:
* ```./config/webpack.dev.js```
* ```./config/webpack.prod.js```

2. 修改```webpack.prod.js```配置, 删除```webpack-dev-server```配置, 修改配置中的路径等.

3. 修改package.json的指令(有时start可能替换为dev)  
    ```json
    scripts: {
     "start": "webpack-dev-server --config ./config/webpack.dev.js",  // 开发环境指令, 不需要先build, 不生成真实dist文件夹
     "build": "webpack --config ./config/webpack.prod.js"  // 生产环境指令
    }
    ```

注意, 生产环境代码需要部署到服务器上才能运行(serve这个库能帮助我们快速搭建一个静态资源服务器)
    * 安装package: ```npm i serve -g```
    * 以dist文件夹为根目录启动服务器: ```serve dist```



### 提取css成单独文件

* ```mini-css-extract-plugin```

### css兼容性处理

* ```postcss-loader```

* ```postcss-preset-env```

### 压缩css

* ```optimize-css-assets-webpack-plugin```


### js兼容性处理

有些浏览器不支持新特性, 需要将浏览器不能识别的新语法转换成原来识别的旧语法(es6->es5),做浏览器兼容性处理.

* ```@babel/core```

* ```babel-loader```

* ```@babel/preset-env```
    * babel能做的事越来越多了, 需要提前告诉它能做什么事

* ```@babel/polyfill```
    * 优点: 解决```babel```只能转换部分低级语法的问题(如: let/const/解构赋值), 引入```polyfill```可以转换高级语法(如: Promise)
    * 缺点: 将所有高级语法都进行了转换, 但实际上可能只使用一部分
    * 解决方案: 需要按需加载(使用了什么高级语法, 就转换什么, 而其他的不转换)

* ```core-js```
    * 可以按需加载

### js压缩

* 生产环境下会自动压缩js代码  
    ```js
    module.exports = {
        ...
        mode: "production"
    }
    ```

### HTML压缩

* 借助```html-webpack-plugin```
    ```js
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    module.exports = {
        ...
        plugins: [
            new HtmlWebpackPlugin({
                template: './src/index.html',
                // 压缩html代码
                mainfy: {
                    // 移除空格
                    collapseWhitespace: true,
                    // 移除注释
                    removeComments: true
                }
            })
        ],
    }
    ```



## 优化配置

### js语法检查

对js基本语法错误, 或是隐患, 进行提前检查

* ```eslint```
    * 基础, 先安装

* ```eslint-loader```
    * 在: eslint.org网站 -> userGuide -> Configuring ESLint 查看如何配置
    * 在: eslint.org网站 -> userGuide -> Rules 查看所有规则
    * 特殊属性
        * ```exclude: /node_modules/```, 排除node_modules文件夹
        * ```enforce: pre```, 提前加载使用(在被其他loader更改前就检查完)
    * 规则不是配置在```webpack.config.js```对应rule中的```options```里, 而是放在package.json的顶级目录下
        ```json
        {
            "name": "",
            "version": "",
            ...
            "eslintConfig": {
                "parserOptions": {
                    "ecmaVersion": 6,  // 支持es6, 不加的话用es6语法会报错
                    "sourceType": "module"  // 使用es6模块化
                },
                "env":{//设置环境
                    "browser": true,  // 支持浏览器环境: 能使用window上的全局变量
                    "node": true  // 支持服务器环境, 能使用node上global的全局变量
                },
                "globals": {  // 声明使用的全局变量, 这样即使没有定义也不会报错了
                    "$": "readonly"
                },
                "rules": {  // eslint检查的规则(覆盖默认规则). 0 忽略, 1 警告, 2 错误
                    "no-console": 0,  // 源代码有console忽略报错
                    "eqeqeq": 2,  //用==而不用===就报错
                },
                "extends": "eslint:recommended"  // 其他的使用eslint推荐的默认规则 https://cn.eslint.org/docs/rules/
            }
        }
        ```
* ```eslint-config-airbnb-base```(可选)

* ```eslint-plugin-import```(可选)

### HMR

HMR(Hot Module Replacement, 热模替换, 模块热更新)可以实现局部刷新(对比webpack-dev-server自带的live reload, 真个页面刷新). 它允许在运行时更新所有类型的模块, 而无需完全刷新(只更新变化的模块, 不变化的模块不更新).

* ```webpack-dev-server```
    * [前置知识](#自动编译打包运行)
    * 详细配置见官网: Guides -> Hot Module Replacement
    * 修改```webpack.config.js```中的devServer配置  
        ```js
        devServer: {
            open:  true,  // 自动打开浏览器
            compress: true,  // 启动gzip压缩
            port:　3000, // 端口号
            hot: true  // 开启HMR
        }
        ```
    * **html文件不支持HMR.** 要想监测到主页面的更新. webpack 默认只能监测到入口文件中改变的东西, 除了```index.js```外, 还需要在入口中加上```index.html```. 这样一来, 更新js和css等模块时就会使用HMR, 更新主页面时就会刷新整个页面.
        ```js
        entry: ['./src/js/index.js', './src/index.html']
        ```

### source-map

我们发布到生产环境的代码，一般都有如下步骤：

* 压缩混淆，减小体积
* 多个文件合并，减少HTTP请求数
* 通过编译或者转译，将其他语言编译成JavaScript

这三个步骤，都使得实际运行的代码不同于开发代码，不管是 debug 还是捕获线上的报错，都会变得困难重重。

解决这个问题的方法，就是使用sourceMap。

简单说，sourceMap就是一个文件，里面储存着位置信息。仔细点说，这个文件里保存的，是转换后代码的位置，和对应的转换前的位置。有了它，出错的时候，通过断点工具可以直接显示原始代码，而不是转换后的代码。

* ```devtool```
    * 是一种将压缩/编译文件中的代码映射回源文件中的原始位置的技术, 让我们调试代码不再困难
    * 详细配置见[官网](https://webpack.js.org/configuration/devtool/): Configuration -> devtool
        * eval: package生成的代码(每个模块彼此分开, 并使用模块名称进行注释), 编译速度快
        * inline: 以base64方式将source-map嵌入到代码中, 缺点是编译后代码体积很大.
        * cheap: 只保留行, 编译速度快
    * 直接在```webpack.config.js```中配置  
        ```js
        module.exports= {
            ...
            devtool: 'cheap-module-eval-source-map'
        }
        ```
    * 推荐使用(学习React的配置)
        * 开发环境: ```eval-cheap-module-source-map```
        * 生产环境: ```cheap-module-source-map```

### oneOf

### 缓存

### tree shaking

### code-split

### lazy-loading

### pwa

### 多进程打包

### externals


---

参考资料:

* [webpack官网](https://webpack.js.org/)
* [尚硅谷webpack从入门到精通(含实现各个目的的plugin和loader的配置)]({{page.resource_path}}/webpack.pdf)
* [Url-loader vs File-loader Webpack](https://stackoverflow.com/questions/49080007/url-loader-vs-file-loader-webpack)
* [Error: Cannot find module 'webpack-cli/bin/config-yargs'](https://github.com/webpack/webpack-dev-server/issues/2029)
* [【JS基础】sourceMap是个啥(含原理)](https://segmentfault.com/a/1190000020213957)