---
title: 'webpack: 常用loader与plugin'
categories: ['前端']
tags: ['javascript', '前端', 'webpack', 'loader', 'plugin']
resource_path: /blog/assets/2020/
---

# webpack: 常用loader与plugin

webpack能直接加载的只有js和json, 要想加载其他资源要使用loader, 想要更多功能需要plugin

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

## loader的使用

1. 安装
    * ```npm install xxx-loader -D```

2. 修改```webpack.config.js```
    * 从官网该loader相应页面将rule复制过来
    * ```js
      module.exports = {
          ...
          module: {
              rules: [
                  {
                      test: ...
                      loader: ...
                  }
              ]
          }
      }
      ```



## plugin的使用

1. 安装
    * ```npm install xxx-plugin -D```

2. 修改```webpack.config.js```
    * 从官网该plugin相应页面将配置复制过来
    * ```js
      const MyPlugin = require("my-plugin")

      ...
        
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

* ```css-loader```

* ```less-loader```(可选)

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

* ```eslint-loader```
    * 要同时安装```eslint```

* ```eslint-config-airbnb-base```

* ```eslint-plugin-import```

### js兼容性处理

有些浏览器不支持新特性, 需要做兼容性处理.

* ```babel-loader```

* ```@babel/core```

* ```@babel/preset-env```

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

* []()

