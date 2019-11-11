---
title: 第一个flutter应用
date: 2019-11-07 11:21:22
tags: flutter
---
1、第一个flutter应用根据官网api模仿了一个登录

![](http://image.gxx365.com/qw.jpeg)

<!-- more --> 
## 先说一下写flutter 和写h5的体会吧 



1、flutter在配置上就是比较麻烦需要配置安卓模拟器和ios模拟器和一些sdk，相对于H5来说H5只需要一个node环境和一个浏览器就能愉快的开发了
2、flutter是用datr语言写是面向对象的语法的，相对于写js的前端同学来说是有一些困难的 里面有一些`泛型` `class` 语法（写过`typescript`的就上手比较容易了）
3、这个小的登录主要包含了flutter的跳转路由传参数、请求http接口判断账号密码、获取Input文本接口传参

## 开始贴代码

1.引入项目依赖文件

```javascript
import 'dart:convert' as convert;
import 'package:flutter/material.dart';
import './http.dart';

```
2.这是我的依赖文件直接复制进去会自动下载包，注意代码缩进和冒号后的空格dart不加空格就会报错项目规范很严谨（经常写js的可能不太习惯😂😂）

```dart
    dependencies:
        flutter:
            sdk: flutter
        dio: ^1.0.14
        json_annotation: ^2.0.0

        # The following adds the Cupertino Icons font to your application.
        # Use with the CupertinoIcons class for iOS style icons.
        cupertino_icons: ^0.1.2
        english_words: ^3.1.0
        http: ^0.11.3+16

    dev_dependencies:
        flutter_test:
            sdk: flutter
        build_runner: ^1.1.3
        json_serializable: ^2.0.1

```

3.这里就是一个根组件 相当于 （react框架的ReactDOM.render挂载到dom）
```dart
    void main() => runApp(MyApp());
    class MyApp extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return MaterialApp(
        title: '登录',
        theme: ThemeData(primaryColor: Colors.white), //主题
        home: Scaffold( 
            appBar: AppBar(
            leading: BackButton(color: Colors.grey), //左边返回Icon
            title: Text('登录'),
            ),
            body: Input(),
        ),
        );
    }
    }
```
4.生成的Input组件放到MyApp中的
```dart
   class Input extends StatefulWidget {
    @override
    _InputState createState() => _InputState();
}
```
5._InputState组件挂载到上面Input里面（因为这个博客是flutter写完之后才编写的所有这些主要代码都放到这里了,但是重要的地方在组件中都有注释）
```dart
class _InputState extends State<Input> {
  final TextEditingController _user = new TextEditingController(); //相当于创建一个控制器接收user的值
  final TextEditingController _pwd = new TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Container( //容器组件里面可以使用Padding,Margin等函数
      margin: const EdgeInsets.fromLTRB(0, 20.0, 0, 0),//参数值对应的四个方向 左、上、右、下
      child: Column( //flutter的布局方式 Column是上下排列 Row在一行排列
        children: <Widget>[
          Container(
            child: Row(children: [
              Container(
                padding: const EdgeInsets.fromLTRB(8.0, 4.0, 0, 0),
                child: Text(
                  '用户名：',
                  style: new TextStyle(color: Colors.black, fontSize: 18),
                ),
              ),
              Container(
                  width: 300,
                  child: Theme(
                    //设置主题 边框线等等。。。
                    data: new ThemeData(primaryColor: Colors.green),
                    child: TextField( //设置文本 输入框
                      controller: _user, //控制器挂载到controller
                      keyboardType: TextInputType.text, //键盘类型
                      cursorColor: Colors.grey, //光标颜色
                      decoration: InputDecoration(hintText: '请输入用户名'),
                    ),
                  )),
            ]),
          ),
          Container(
            child: Row(children: [
              Container(
                padding: const EdgeInsets.fromLTRB(26, 4.0, 0, 0),
                child: Text(
                  '密码：',
                  style: new TextStyle(color: Colors.black, fontSize: 18),
                ),
              ),
              Container(
                  width: 300,
                  child: Theme(
                    data: new ThemeData(primaryColor: Colors.green),
                    child: TextField(
                      controller: _pwd,
                      cursorColor: Colors.grey,
                      keyboardType: TextInputType.text, //键盘类型
                      textInputAction: TextInputAction.done,
                      obscureText: true, //控制文本是否以加密形式展示
                      decoration: InputDecoration(hintText: '请输入密码'),
                    ),
                  )),
            ]),
          ),
          Container(
            width: 200,
            margin: const EdgeInsets.fromLTRB(0.0, 20.0, 0, 0),
            child: RaisedButton( //按钮组件
              onPressed: () {
                  //点击按钮的执行事件
                Map<String, String> map = new Map(); //创建一个类型数据给接口传参
                var pwd = _pwd.text; 
                var user = _user.text;
                map["user"] = user;
                map["pwd"] = pwd;
                var obj = convert.jsonEncode(map);//参数转化相当于JSON.stringfy

                //接口请求 这个封装在别的组件 下面会有 
                HttpController.post("http://localhost:8002/login", (data) {
                  if (data != null) {
                    var res = convert.jsonDecode(data); //解码
                    var code = res['code'];
                    if (code == 1) {
                    //跳转路由
                      Navigator.push(context, 
                          MaterialPageRoute(builder: (context) => MyHome()));
                    }
                  }
                }, params: obj);
              },
              child: Text('登录'),
              textColor: Colors.white,
              color: Colors.blue,
              shape: RoundedRectangleBorder(
                  borderRadius: BorderRadius.circular(4.0)),
            ),
          )
        ],
      ),
    );
  }
}
```
6.跳转的首页组件
```dart
 class MyHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
          leading: BackButton(color: Colors.grey),
          title: Text('首页'),
        ),
        body: Center(
          child: RaisedButton(
            child: RaisedButton(
              child: Text('返回登录页面'),
              onPressed: () {
                Navigator.pop(context);//返回上一页
              },
            ),
          ),
        ));
      } 
    }
```


7.请求http组件
```dart
import 'package:http/http.dart' as http;
 
class HttpController {
  static void get(String url, Function callback,
      {Map<String, String> params, Function errorCallback}) async {
    if (params != null && params.isNotEmpty) {
      StringBuffer sb = new StringBuffer("?");
      params.forEach((key, value) {
        sb.write("$key" + "=" + "$value" + "&");
      });
      String paramStr = sb.toString();
      paramStr = paramStr.substring(0, paramStr.length - 1);
      url += paramStr;
    }
    try {
      http.Response res = await http.get(url);
      if (callback != null) {
        callback(res.body);
      }
    } catch (exception) {
      if (errorCallback != null) {
        errorCallback(exception);
      }
    }
  }
 
  static void post(String url, Function callback,
      {params, Function errorCallback}) async {
    try {    
        //记得content-type和后台服务统一                                           
      http.Response res = await http.post(url,headers: {"content-type": "application/json"}, body: params);
 
      if (callback != null) {
        callback(res.body);
      }
    } catch (e) {
      if (errorCallback != null) {
        errorCallback(e);
      }
    }
  }
}
```


### 最后总结一下

1、刚开始上手flutter感觉挺蒙但是写下来之后其实还是一些函数的调用里面的大多数功能都会封装在函数里面调用传参执行的 
2、样式上写起来比较乱没有h5写起来整洁但是相对于ios和安卓开发还是有很多优点的，上手程度来说也容易一点而且比较重要的是`跨平台`一套代码两端使用
3、而且flutter是谷歌维护的 相信以后会越来越完美的