---
title: 'webpack: 常用loader与plugin'
categories: ['前端']
tags: ['javascript', '前端', 'webpack', 'loader', 'plugin']
resource_path: /blog/assets/2020/10/16/loaders_plugins
---

# webpack: 常用loader与plugin

webpack能直接加载的只有js和json, 要想加载其他类型的资源(样式文件, 图片文件等)要使用loader, 想要更多功能(打包优化, 压缩等)需要plugin.

加载资源不再与```index.html```对话, 不再通过标签引入. 而是与 entry points (入口js文件)对话, 通过```import```引入资源.


* [如何找到合适的plugin和loader](#如何找到合适的plugin与loader)

* [loader的使用](#loader的使用)

* [plugin的使用](#plugin的使用)

* [开发环境的基本配置](#开发环境的基本配置)
    * [打包样式资源](#打包样式资源)
    * [打包HTML资源](#打包HTML资源)
    * [打包图片资源](#打包图片资源)
    * [打包其他资源](#打包其他资源)
    * [devserver](#devserver)

* [生产环境的基本配置](#生产环境的基本配置)
    * [提取css成单独文件](#提取css成单独文件)
    * [css兼容性处理](#css兼容性处理)
    * [压缩css](#压缩css)
    * [js语法检查](#js语法检查)
    * [js兼容性处理](#js兼容性处理)
    * [js压缩](#js压缩)
    * [HTML压缩](#HTML压缩)

* [优化配置](#优化配置)
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
 
### 打包样式资源

* ```style-loader```  
    * 用于在html文档中创建一个style标签, 将样式塞进去

* ```css-loader```  
    * 将css转换为CommonJS模块

* ```less-loader```(可选)  
    * 要配合安装less使用
    * 将less编译为css, 但不生成单独的css文件, 在内存中

* 在js文件中导入样式文件  
    ```js
    import "index.css"
    ```

### 打包HTML资源

webpack不能解析html文件, 需要借助插件编译解析

* ```html-webpack-plugin```
    * 注意与**html-loader**区分开, 后者用于处理html中的标签资源

### 打包图片资源

* ```file-loader```

* ```url-loader```

* ```html-loader```
    * html中的图片url-loader没法处理, 它只能处理js中引入的图片/样式中的图片, 不能处理html中img标签.
    * 该loader可以处理html中的标签资源, 不止img标签)

* Url-loader vs File-loader
    * file-loader will copy files to the build folder and insert links to them where they are included. url-loader will encode entire file bytes content as base64 and insert base64-encoded content where they are included. So there is no separate file.

### 打包其他资源
    
* 


### devserver

* ```devserver```


## 生产环境的基本配置

### 提取css成单独文件

* ```mini-css-extract-plugin```

### css兼容性处理

* ```postcss-loader```

* ```postcss-preset-env```

### 压缩css

* ```optimize-css-assets-webpack-plugin```

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

### HMR

### source-map

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

* [尚硅谷webpack从入门到精通]({{page.resource_path}}/webpack.pdf)
* [Url-loader vs File-loader Webpack](https://stackoverflow.com/questions/49080007/url-loader-vs-file-loader-webpack)
