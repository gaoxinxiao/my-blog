---
title: 从0搭建webpack需要注意的问题
date: 2019-10-16 17:17:59
tags:
- webpack
---

1.webpack是对于现如今的web来说是一个不可或缺的东西 
2.针对项目优化底层构建都需要对webpack比较熟悉所以今天总结一下 
3.这篇博客主要提及一下在搭建webpack的过程中可能会遇到的问题以后会出完整版的webpack构建

<!-- more -->
### 搭建过程注意事项
 1.首先在搭建的过程中一定要保证包的版本都是对应的 比如 babel-loader的版本是8点几的话 对应的 babel-core就是版本7点几
 2.有时候会报错您需要一个loader来加载当前的文件多半是 用less 或者sass的时候没有配loader 或者在配置其他语法 如tsx ts等 也会提示 去找对应的loader配置上即可
 3.在src下 如果想要引入外部文件的话 需要更改ModuleScopePlugin 或者直接把包删掉就可以这条只限于官方脚手架 （估计react设置这个的目的 就是不让你乱引入目录）

```` javascript
    const ModuleScopePlugin = require('react-dev-utils/ModuleScopePlugin');
    new ModuleScopePlugin([paths.appSrc, paths.appPublic], [paths.appPackageJson, paths.webConfig]),
````
 4、如果自己搭建的脚手架 按照官网配置完成还没有热更新的话 可能是在entry少放了 require.resolve('react-dev-utils/webpackHotDevClient') 加上就好了 配置热加载地址
  https://www.jianshu.com/p/dda3c7940b92 
 5、如果使用typescript开发的时候 配置公共路径直接用tsconfig.json的baseurl映射到webpack 的alias就可以不需要在webpack里重新配置alias
 6、@babel/preset-env 转化es6但是有一些新的语法不能转化 例如 Generator函数 和Array.From等方法 
 7、转换这些方法可以用@babel/polyfill 但是使用@babel/polyfill会导致包很大 可以使用@babel/plugin-transform-runtime 避免 polyfill 污染全局变量，减小打包体积 同时也要下载@babel/runtime并且在babelrc plugins引入 @babel/plugin-transform-runtime
 8.我只用了@babel/plugin-transform-runtime里面的一个工具useBuiltIns 这个是按需引入 如果设置为true就会在项目中强制引入 babel-polify但是babel-polyfill只能被引入一次，如果多次引入会造成全局作用域的冲突。
```` javascript
        "presets": [
            [
                "@babel/preset-env",
                {
                    "useBuiltIns": "usage"
                }
            ]
        ]
````
 9、下载 core-js@2 @babel/runtime-corejs2到dependencies中 配合babelrc useBuiltIns: usage
 10、babel-polyfill主要是通过修改全局prototype来实现API的垫片的，所以适合在单独的项目中


### 说一下详细的热加载配置
 1、在webpack devserver启动 hot:true 
 2、plugins中增加new webpack.HotModuleReplacementPlugin()
 3、npm install -D react-hot-loader@4.5.3 （我用过12.几的包 但是不起作用）
 4、根目录引入import { AppContainer } from "react-hot-loader"在render 里多包一层AppContainer
 5、引入组件 `初始化` renderWithHotReload(Router)
```` javascript
    //热更新
    if (module.hot) {
        module.hot.accept("./router/index.js", () => {
            const Router = require("./router/index.js").default;
            renderWithHotReload(Router);
        });
    }

    function renderWithHotReload(Router) {
        ReactDOM.render(
            <AppContainer>
                <BrowserRouter>
                    <Router />
                </BrowserRouter>
            </AppContainer>,
            document.getElementById("root")
        );
    }
````
6、还有比较重要的一步 在.babelrc的plugins下增加"react-hot-loader/babel"


### webpack优化记录
1、从js中拆分css在第一次页面加载的时候能减少js的加载
2、压缩css，js在webpack4的mode中设置production会自动压缩
3、处理一些字体和图片的时候可以设置一个图片或者字体的大小如果符合设置limit就打包成base64形式加载减少http请求
4、可以使用purifycss-webpack清除一些无用的css但是要`慎用`因为可能会清楚掉有用的css
5、设置devtool:cheap-module-source-map使js都有map版（压缩成最小的）
6、splitChunks 按需加载所有的js
7、设置usedExports清楚无用的js
```` javascript
      optimization: {
        splitChunks: {
            chunks: "all",
        },
        usedExports: true //清楚代码中无用的js 
    }
````
8、设置一个文件专门打包公共的第三方插件比如jquery或者lodash等 因为第三方文件不是每一次都会更改的不需要每次都打包可以很大的提升打包速度 通过add-asset-html-webpack-plugin引入打包后的第三方文件
通过 webpack.DllReferencePlugin引入第三方json
9、在生产环境打包js的时候在出口设置成contenthash这样可以当每次文件没有更改的时候不会重新打包提升打包速度
```` javascript
     output: {
        filename: 'static/js/[name].[contenthash].js',
        chunkFilename: 'static/js/[name].[contenthash].js'
    }
````
10、因为webpack是单线程执行的可以使用happypack提升进程（但是这个目前还没用明白🤔）

### 以上是对webpack简单的总结 如果那里有问题 可以给发邮件讨论