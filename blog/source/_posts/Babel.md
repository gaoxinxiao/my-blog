---
title: Babel相关知识
date: 2019-12-17 13:49:41
tags:
---

>![](http://image.gxx365.com/babel.jpeg)
那时，天下人的口音，言语，都是一样。
他们往东边迁移的时候，在示拿地遇见一片平原，就住在那里
他们彼此商量说，来吧，我们要作砖，把砖烧透了。他们就拿砖当石头，又拿石漆当灰泥。
他们说，来吧，我们要建造一座城和一座塔，塔顶通天，为要传扬我们的名，免得我们分散在全地上
耶和华降临，要看看世人所建造的城和塔。
耶和华说，看哪，他们成为一样的人民，都是一样的言语，如今既作起这事来，以后他们所要作的事就没有不成就的了。
我们下去，在那里变乱他们的口音，使他们的言语彼此不通。
于是，耶和华使他们从那里分散在全地上。他们就停工，不造那城了。
因为耶和华在那里变乱天下人的言语，使众人分散在全地上，所以那城名叫巴别（就是变乱的意思）
“Babel”的原意就是指人类为了见到上帝，准备建造一座通天塔，上帝为了阻止人类，让他们语言变的不同。于是，人类开始有了文化差异，从而导致了冲突……


<!-- more --> 

## 本文的目的是了解一下babel常用的插件以及使用姿势但是不包括编译原理（AST）

 <p style='background:#ccc;color:#333;border-left:4px solid skyblue;padding:8px'>重点：Babel是一个js的编译器主要是我们在项目中遇到的低版本浏览器识别不了的高级语法和函数方法编译成低版本浏览器可识别的
</p>

### 下面开始我们的操作 

```javascript
    创建一个文件夹babel（随便叫啥）
    npm init -y 创建package.json
    随便写点高级语法比如 箭头函数 const fun = () =>{console.log('hello world')}
```


### 创建完项目介绍几个知识点

- 核心库@babel/core
- babel的核心在于这个插件core代表核心，没有这个其他插件也没办法使用所以他的重要性知道了吧


<h3 style='border-bottom:1px solid purple'><span style='background:purple;color:white;font-size:20px;padding:6px;border-top-left-radius:6px;border-top-right-radius:6px'>@babel/cli</span></h3>

 - babel提供的命令行工具，提供babel执行命令 
 - 虽然你可以在你的计算机上将 Babel CLI 安装到全局环境中，但是更好的方式是 将 Babel CLI 安装到每个项目的 本地 目录下。
 > 这主要有两个原因：
    1.同一台计算机上的不同项目可能依赖不同版本的 `Babel` ，并且你可以针对项目单独升级 Babel 的版本。
    2.没有对你所正在工作的环境的隐性依赖 能够让你的项目更易于迁移和设置。

### 配置 package.json
```javascript
    //编译src/app.js，编译完的目录为dist --watch 监听文件变化自动修改
   "scripts": {
        "compile": "babel src/app.js --out-dir dist --watch" 
    }
```
执行命令 npm run compile 编译app.js 你看到的输出结果是一样的这正常，因为我们没有装任何的编译插件，往下看

![](http://image.gxx365.com/不要慌.jpg)


### 安装插件
1.npm i -D @babel/plugin-transform-arrow-functions //这个插件专门转换箭头函数
2.新建一个.babelrc文件
3.配置.babelrc
```javascript
    {
        "plugins":["@babel/plugin-transform-arrow-functions"]
    }   
```
重新编译 npm run compile  这个时候看到js已经是 普通function

> 但是现在只支持箭头函数如果想支持其他高级语法还需要一个一个配置别的`plugins`这显然很麻烦有没有不用自己一个一个配置的，有请看下面！！！


### 预设插件

官方`preset`

>@babel/preset-env
 @babel/preset-flow
 @babel/preset-react
 @babel/preset-typescript

提示: 从 `Babel v7` 开始，所以针对标准提案阶段的功能所编写的预设(stage preset)都已被弃用，官方已经移除了 `@babel/preset-stage-x`。

<h3 style='border-bottom:1px solid purple'><span style='background:purple;color:white;font-size:20px;padding:6px;border-top-left-radius:6px;border-top-right-radius:6px'>@babel/preset-env</span></h3>

- @babel/preset-env 主要作用是对我们所使用的并且目标浏览器中缺失的功能进行代码转换和加载 polyfill，在不进行任何配置的情况下，
- @babel/preset-env 所包含的插件将支持所有最新的JS特性(ES2015,ES2016等，不包含 stage 阶段)，将其转换成ES5代码。
- 例如，如果你的代码中使用了可选链(目前，仍在 stage 阶段)，那么只配置 @babel/preset-env，转换时会抛出错误，需要另外安装相应的插件。

![](http://image.gxx365.com/顶呱呱.jpg)


> 下载插件

1.npm i -D @babel/preset-env
2.配置.babelrc
```javascript
    {
        "presets":["@babel/preset-env"]
    } 
```
3.会根据你配置的目标环境，生成插件列表来编辑，默认情况下如果没有在.babelrc的targes设置编译环境的话，会默认使用browserslist配置

如果不是兼容所有浏览器环境，指定目标环境这样的编译代码会更小
例如，仅包括浏览器市场份额超过1％的用户所需的和每个浏览器向后兼容两个版本的和方向排除的部分版本 polyfill 代码转换
> //.browserslistrc
">1%",
"last 2 versions",
"not ie <=8"

设置package.json 
```javascript
    "browserslist":[
        "last 2 Chrome versions"
    ]
```
执行 npm run compile 发现箭头函数不被编译了 因为谷歌浏览器后两个版本支持js新语法


修改一下app.js

```javascript
    [ 1,2,3].includes(1);
    new Promise((resolve,reject)=>{
        resolve(6666);
    });
    Array.from([12,3]);
```
执行 npm run compile 编译，发现编译之后的app.js是一样的，这显然无法在低版本浏览器中使用因为低版本浏览器没有Promise构造函数更没有incudes方法

 <p style='background:#ccc;color:#333;border-left:4px solid skyblue;padding:8px'>重点：因为设置@babel/preset-env的时候并没有设置兼容最新方法这时候就需要配置polyfill，这个单词翻译过来就是垫片，
    意思就是垫平每个浏览器不一样的地方，让新的内置函数方法可以在低版本浏览器中使用（我是这么理解的）</p>

 ### 下载相关插件

 npm install --save core-js@3 
 配置.babelrc
```javascript
    {
        "presets": [
            [
                "@babel/preset-env",
                {
                    "useBuiltIns": "usage",
                    "corejs": 3
                }
            ]
        ]
    }
```

> 注:这里说一下使用上面这个方法得配置一个对应的 core-js@3 ，core-js@2的分支上已经不会在加新特性了，新特性都会加到 core-js@3


执行编译 npm run compile

此时app.js 代码

```javascript
   [1,2,3].includes(1);
        new Promise((resolve,reject)=>{
            resolve(6666);
        });
        Array.from([12,3]);
    }
```
编译之后的 
```javascript
   "use strict";

    require("core-js/modules/es.array.from");

    require("core-js/modules/es.array.includes");

    require("core-js/modules/es.object.to-string");

    require("core-js/modules/es.promise");

    require("core-js/modules/es.string.iterator");

    [1, 2, 3].includes(1);
    new Promise(function (resolve, reject) {
    resolve(6666);
    });
    Array.from([12, 3]);
```

可以看到编译完的代码已经把缺失的功能都引进来了，但是这些是注入到全局下的会污染全局环境所以推荐另一种方式：@babel/plugin-transform-runtime

<h3 style='border-bottom:1px solid purple'><span style='background:purple;color:white;font-size:20px;padding:6px;border-top-left-radius:6px;border-top-right-radius:6px'>@babel/plugin-transform-runtime</span></h3>

新增依赖
- npm install --save-dev @babel/plugin-transform-runtime
- npm install --save @babel/runtime
- npm install @babel/runtime-corejs3 --save

> @babel/plugin-transform-runtime可以创造一个环境，如果使用@babel/preset-env的polyfill及其提供的内置程序例如 Promise ，Set 和 Map等，虽然这个对于当前的应用程序可能是可以的，但是你要是作为发布给他人使用的库这将成为一个问题

### 更改.babelrc
1.删除@babel/preset-env的corejs
2.新增@babel/transform-runtime corejs

```javascript
    {
        "presets": [
            [
                "@babel/preset-env"
            ]
        ],
        "plugins": [
            [
                "@babel/transform-runtime",{
                    "corejs":3
                }
            ]
        ]
    }
```

执行编译 npm run compile
```javascript
    "use strict";

    var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");

    var _from = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/array/from"));

    var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));

    var _includes = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/instance/includes"));

    var _context;

    (0, _includes["default"])(_context = [1, 2, 3]).call(_context, 1);
    new _promise["default"](function (resolve, reject) {
    resolve(6666);
    });
    (0, _from["default"])([12, 3]);
```
可以看到没有直接去修改 Array.prototype，或者是新增 Promise 方法，避免了全局污染
 <p style='background:#ccc;color:#333;border-left:4px solid skyblue;padding:8px'> 重点：我们配置的是@babel/runtime-corejs3不管是实例方法还是全局方法都不会污染全局</p>
   

###  插件/预设知识

> 插件的排列顺序很重要！！！

1.Plugins在Presets前运行
2.Plugins顺序从前往后排
3.Preset顺序是颠倒执行（从后往前）
```javascript
     {
        "presets": ["@babel/preset-env", "@babel/preset-react"]
    }
```
执行顺序 "@babel/preset-react" ,"@babel/preset-env"
   

####  插件可以使用缩写以下两个是一样的效果

如果插件名称为 @babel/plugin-XXX，可以使用短名称@babel/XXX 
```javascript
    "plugins": [
            [
                "@babel/plugin-transform-runtime",{
                    "corejs":3
                }
            ]
        ]
```
```javascript
    "plugins": [
        [
            "@babel/transform-runtime",{
                "corejs":3
            }
        ]
    ] 
```

  最后  谢谢 end