---
title: 'webpack: 常用loader与plugin'
categories: ['前端']
tags: ['javascript', '前端', 'webpack']
resource_path: /blog/assets/2020/10/16/loaders_plugins
---

# webpack: 常用loader与plugin

- [webpack: 常用loader与plugin](#webpack-常用loader与plugin)
  - [loader 与 plugin 概述](#loader-与-plugin-概述)
    - [为什么需要 loader 和 plugin](#为什么需要-loader-和-plugin)
    - [不同环境的 loader 与 plugin](#不同环境的-loader-与-plugin)
    - [如何找到合适的 loader 与 plugin](#如何找到合适的-loader-与-plugin)
    - [loader 的使用](#loader-的使用)
    - [plugin 的使用](#plugin-的使用)
  - [开发环境的基本配置](#开发环境的基本配置)
    - [打包样式资源](#打包样式资源)
    - [打包 HTML 资源](#打包-html-资源)
    - [打包图片资源](#打包图片资源)
    - [打包其他资源](#打包其他资源)
    - [自动编译打包运行](#自动编译打包运行)
    - [typescript](#typescript)
    - [sass](#sass)
  - [生产环境的基本配置](#生产环境的基本配置)
    - [提取 css 成单独文件](#提取-css-成单独文件)
    - [css 兼容性处理](#css-兼容性处理)
    - [压缩 css](#压缩-css)
    - [js 兼容性处理](#js-兼容性处理)
    - [js 压缩](#js-压缩)
    - [HTML 压缩](#html-压缩)
  - [优化配置](#优化配置)
    - [js 语法检查](#js-语法检查)
    - [HMR](#hmr)
    - [source-map](#source-map)
    - [清空打包文件目录](#清空打包文件目录)
    - [oneOf](#oneof)
    - [缓存](#缓存)
    - [tree shaking](#tree-shaking)
    - [code-split](#code-split)
    - [lazy-loading](#lazy-loading)
    - [pwa](#pwa)
    - [多进程打包](#多进程打包)
    - [externals](#externals)

---

## loader 与 plugin 概述

### 为什么需要 loader 和 plugin

webpack 是一个模块打包器。它的主要目标是将 JavaScript 文件打包在一起. 加载资源不再与```index.html```对话, 不再通过标签引入. 而是与 entry points (入口js文件)对话, 通过```import```引入资源. 

webpack能直接加载的**只有 js 和 json**, 要想加载其他类型的资源(css, sass, ts, jpg, png 等)要使用loader, 想要更多功能(打包优化, 压缩等)需要plugin.

loader都是官方出品, 配置方法比较规范. plugin就不一定了.

### 不同环境的 loader 与 plugin

一般来说，我们会将配置文件拆成三部分：

1. `webpack.common.js`: 开发环境和生产环境通用配置
2. `webpack.dev.js`: 开发环境配置，用于 `npm start`
3. `webpack.prod.js`: 生产环境配置，用于 `npm run-script build`

可以利用 `webpack-merge` 这个库，帮我们把通用配置，结合到另外两个配置里。

不同环境，使用的 loader 和 plugin 可能会不同

```js
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    //...
    modules: {
        rules:[
            {
                test: /\.css$/i,
                use: [
                    'style-loader',
                    'css-loader'
                ]
            },
            {
                test: /\.html$/i,
                loader: 'html-loader',
            },
        ],
        plugins:[
            new HtmlWebpackPlugin({
                template: './src/index.html'
            })
        ]
    }
}
```

### 如何找到合适的 loader 与 plugin

1. 方法一: 官网上列出了一些优秀的loader与plugin(**以及配置方法**)
    - loader: https://webpack.js.org/loaders/
          * plugin: https://webpack.js.org/plugins/
      * 利用 ctrl+f 在页面中搜索

2. 方法二: 不知道用什么loader或plugin时, 可以参考React和Vue.

3. 方法三: 根据用的工具 (如 typescript，sass) 的需求，借助搜索引擎。


### loader 的使用

注意，在一个 rule 里配置多个 loader 时，执行顺序是从后到前。例如，配置 sass 文件的 loader 时，要把 sass-loader 放到 css-loader 等之后，这样就可以先让 sass-loader 将 sass 文件转为 css，再利用 css 相关 loader 进行进一步的处理。

1. 安装
    * ```npm install -D xxx-loader```

2. 配置(```webpack.config.js```)
    * 暴露loader
      ```js
      module.exports = {
          ...
          module: {
              rules: [
                  {
                      test: /(\.csv)$/  // loader要处理的文件的名字, 一般用正则表达式表示
                      loader: "file-loader"  // loader = loader名字/loader列表/外部引入模块提供的对象/自定义对象(包含loader和option等), 
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



### plugin 的使用

1. 安装
    * ```npm install xxx-plugin -D```

2. 配置(```webpack.config.js```)
    * 引入插件(CommonJS规范)  
      ```js
      const MyPlugin = require("my-plugin");  // 有些可能引入方式略有不同, 需要解构赋值等.
      module.exports = {
          // ...
          plugins: [
              new MyPlugin(params),
          ]
      };
      ```

3.一些 plugin 可能还提供了自己的 loader, 需要 plugin 和 loader 结合使用。 比如[```mini-css-extract-plugin```](#提取css成单独文件)。对于它们提供的loader，按照普通 loader 来配置。

---

## 开发环境的基本配置

在顶层目录创建文件夹```dist```

```webpack.config.js```中的"mode"设为"development"
 
### 打包样式资源

1. 安装 loader
   * ```style-loader```：用于在html文档中创建一个style标签, 将样式塞进去
   * ```css-loader```：将css转换为CommonJS模块, 翻译 `@import` 和 `@url` 并且解析它们。

2. 配置 webpack:
   ```js
    {
        test: /\.css$/i,
        use: [
            'style-loader',
            'css-loader'
        ]
    }
   ```

3. 在入口文件中导入样式文件: 
  ```js
  import "index.css"
  ```
  
注意，不可以加载样式文件中的图片链接. 要加载需要额外的loader, 详见该部分: [打包图片资源](#打包图片资源)

### 打包 HTML 资源

webpack不能解析html文件, 需要借助插件编译解析. 只有打包 html 不是用的 loader 而是plugin, 不走入口文件.

1. 安装loader
    * ```html-webpack-plugin```
        * 注意与**html-loader**区分开, 后者用于处理html中的标签资源
        * 注意不要在 html 中引入任何 css 和 js 文件

2. 配置 webpack
   1. 注意多页面配置好各个页面对应的 chunks， 即 entry point.

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

* 通用loader: ```file-loader```
    * copy files to the build folder and insert links to them where they are included.
    * import 文件，import 进来的是文件路径。然后我们可以接着使用专门处理该类型文件的库，来读取文件以及对文件进行解析。

* 特殊 loader:
    * 如css格式的字体文件需要打包样式文件的那些loader.
    * loader

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

### typescript

1. 安装基本库

    ```cmd
    npm install -D typescript
    ```

2. 在根目录下配置好 ts 的配置文件 `tsconfig.json`. 可参考[官网手册](https://www.typescriptlang.org/tsconfig#isolatedModules) 以及一些开源项目的配置。尤其注意配置好 ts 生效的文件夹。

3. 安装其他库的类型库。比如如果有使用 jest 的话，执行 `npm install -D @types/jest`

4. 安装 loader

    ```cmd
    npm install -D ts-loader
    ```

5. 配置 webpack

   ```js
    {
      test: /\.tsx?$/,
      use: 'ts-loader',
      exclude: /node_modules/,
    },
   ```



### sass 

1. 安装基本库
   
    ```bash
    npm install -D node-sass # provides binding for Node.js to LibSass, a Sass compiler.
    或
    npm install -D sass  # dark sass
    ```

2. 安装 loader

    ```bash
    npm install -D style-loader css-loader sass-loader
    ```

3. 配置 webpack，将 sass-loader 串到 css 相关 loader 的后面。
   ```bash
    {
      test: /\.(scss|sass|css)$/,
      use: ['style-loader', 'css-loader', 'sass-loader'],
    },
   ```

4. 在文件中直接引用
    ```js
    import 'xxx.sass'
    ```

---

## 生产环境的基本配置

本节提到的plugin只安装在```webpack.prod.js```, 不安装在```webpack.dev.js```.

### 提取 css 成单独文件

默认不装插件的情况下, import 样式表, 会直接将其转化为内联样式, 不会生成单独的css文件.

* ```mini-css-extract-plugin```
    * 该插件会将样式文件合并成名为main的样式文件. 
    * 引入插件  
      ```const MiniCssExtractPlugin = require("mini-css-extract-plugin")```
    * 配置loader  
      ```js
      {
          test: /\.less$/,
          use: [
              MiniCssExtractPlugin.loader,  // 取代style-loader
              'css-loader',
              'less-loader'
          ]
      }
      ```
    * 配置plugin  
      ```js
      new MiniCssExtractPlugin({
          filename: "css/[name].css", // 原来的所有css,less等类型的文件, 合并成一个名为main的css文件.
      })
      ```

### css 兼容性处理

* ```postcss-loader```

    * https://webpack.js.org/loaders/postcss-loader/

    * 很多教程只安装这个, 不装下面的一些package. 但是只安装这个对IE兼容性不太好

    * 需要配合其他针对于该loader的plugin使用(注意不是webpack总的pulgin, 只在该loader的option内配置). 目前有些还不支持postcss8.
        * ```postcss-preset-env```: 指定运行环境
        * ```postcss-flexbugs-fixes```
        * ```postcss-normalize```: 加上后对老浏览器的支持比较好, react在用
        * ```autoprefixer```

    * 配置loader(postcss8及之后的版本不适用) 
      ```js
      {
        loader: 'postcss-loader',
        options: {
          postCssOptions: {
            ident: 'postcss',
            plugins: () => [
              require('postcss-flexbugs-fixes'),
              require('postcss-preset-env')({
                autoprefixer: {
                  flexbox: "no-2009"  // flex老语法新浏览器反而不支持
                },
                stage: 3,  // 兼容级别
              }),
              require('postcss-normalize')(),
            ]
          }
        },
      }
      ```
    * 添加配置文件: ```.browserslistrc```(直接在package.json中顶级目录配置```browserlist```的话, 有些文件可能会失效)
      ```
      last 1 version
      > 1%
      IE 10 # sorry
      ```



### 压缩 css

调成production模式只能压缩js, 不能压缩css. 想要压缩css, 我们需要借助其他的插件.

* ```optimize-css-assets-webpack-plugin```
    * 压缩css的插件有很多, vue和react里用的是这个
    * 引入插件  
        * ```const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");```
    * 配置插件(React中的配置)
        * ```js
          new OptimizeCssAssetsPlugin({
            cssProcessorPluginOptions: {
              preset: ['default', { descardComments: { removeAll: true } }],
            },
            cssProcessorOptions: {
              map: {
                inline: false,
                annotation: true,
              }
            }
          })
          ```


### js 兼容性处理

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

### js 压缩

* 生产环境下会自动压缩js代码  
    ```js
    module.exports = {
        ...
        mode: "production"
    }
    ```

### HTML 压缩

* 借助```html-webpack-plugin```
    ```js
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    module.exports = {
        ...
        plugins: [
            new HtmlWebpackPlugin({
                template: './src/index.html',
                // 压缩html代码
                minify: {
                    removeComments: true,
                    removeRedundantAttributes: true,  // 移除无用的标签
                    removeEmptyAttributes: true,  // 移除空标签
                    removeStyleLinkTypeAttributes: true,  // 移除rel="stylesheet"

                    /* html文档中可能有嵌入js,css等, 精简它们 */
                    minifyJS: true, 
                    minifyCSS: true,
                    minifyURLs: true,

                    collapseWhitespace: true,
                    useShortDoctype: true,  // 使用短的文档申明
                    keepClosingSlash: true,  // 自结束
                }
            })
        ],
    }
    ```

---

## 优化配置

### js 语法检查

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

### 清空打包文件目录

在与他人协作编程时, 其他人可能会不小心直接向打包文件夹中添加文件, 这样的话该文件在下次重新打包后并不会被删除. 每次打包生成了新的文件, 都需要手动删除之前的文件, 引入插件帮助我们自动删除上一次的文件.

* ```clean-webpack-plugin```
    * 引入插件(需要解构赋值)
        * ```const {CleanWebpackPlugin} = require("clean-webpack-plugin");```

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

* [webpack-quick-starter (个人的，不完善)](https://github.com/VirusPC/webpack-quick-starter)
* [webpack官网](https://webpack.js.org/)
* [尚硅谷webpack从入门到精通(含实现各个目的的plugin和loader的配置)]({{page.resource_path}}/webpack.pdf)
* [Url-loader vs File-loader Webpack](https://stackoverflow.com/questions/49080007/url-loader-vs-file-loader-webpack)
* [Error: Cannot find module 'webpack-cli/bin/config-yargs'](https://github.com/webpack/webpack-dev-server/issues/2029)
* [【JS基础】sourceMap是个啥(含原理)](https://segmentfault.com/a/1190000020213957)
* [Template strings](https://webpack.js.org/configuration/output/#template-strings)
* [Setting up CSS and Sass with Webpack!!](https://dev.to/deepanjangh/setting-up-css-and-sass-with-webpack-3cg)
* [How to configure SCSS modules for Webpack](https://www.developerhandbook.com/webpack/how-to-configure-scss-modules-for-webpack/)
* [TSConfig Reference](https://www.typescriptlang.org/tsconfig#isolatedModules)
* [Configuring Jeste](https://jestjs.io/docs/configuration)