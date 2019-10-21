---
title: 移动端绝对底部
date: 2019-10-16 16:55:39
tags:
- HTML
---
今天遇到一个需求实现移动端绝对底部
刚开始想用js来实现后来发现用html+css就可以搞定

<!-- more -->
###  1、布局HTML
感觉移动端大多数布局都是这样的🤔

```` javascript
    <body>
        <header>
            <h1>我是头部</h1>
        </header>
        <div class="main">
            <p>我是内容区</p>
        </div>
        <footer>
            <p>我是底部</p>
        </footer>
    </body>
````

### 2、css布局
主要就是body开弹性盒

```` javascript
   body {
        display: flex;
        flex-flow: column;
        min-height: 100vh;
        overflow: auto;
    }
    .main {
        flex: 1;
    }
````