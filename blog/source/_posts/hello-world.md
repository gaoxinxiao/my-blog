---
title: js数组遍历方法整理
date: 2018-05-11 12:30:57
categories:  
- JavaScript
tags:
- 数组
---
js数组遍历是在数组操作中经常用到的,整理一下！
![](http://image.icey.cc/2018-10-07-003821.jpg)
<!-- more -->

### 1、for循环
效率应该是最高的吧

```` javascript
        // 假设一个数组 
        let arr = [1,2,3]
        arr.a = '我是arr的私有属性'    
        // for循环
        for(var i=0;i<arr.length;i++){
            // do something
            console.log(arr[i])
        }
        // => 1  2  3
````

### 2、forEach循环
forEach循环的参数有两个，第一个为参数为实际操作的函数function，第二个参数that是改变this指向的，一般也很少用。
forEach参数的函数中也有两个参数，第一个是循环的每一项item，第二个参数是每一项对应的下标index。
注意，使用中，that和index均可不写不用,而且forEach循环不支持return

```` javascript
        // 假设一个数组 
        let arr = [1,2,3]
        arr.a = '我是arr的私有属性'
        forEach
        let that = {name:'jake'}
        arr.forEach(function(item,index){
            console.log(item)
            console.log(this) // 本来应该是指向window的，添加第二个参数后，this指向改变了
        },that)
        // => 1 {name: "jake"}   2 {name: "jake"}  3 {name: "jake"}

````

### 3、for in 循环
不常用。主要用于对象的遍历，可以遍历出私有属性。遍历数组时也会遍历私有属性
```` javascript
        let arr = [1,2,3]
        arr.a = '我是arr的私有属性'
        // for in 
        // 假设一个对象
        let Obj = {
            name: 'jake',
            age: 19
        }
        Obj.hi = '你好'
        for(let key in Obj){
            console.log(key +':'+Obj[key])
        }
        // => name:jake   age:19   hi:你好

        for(let key in arr){
            console.log(key +':'+arr[key])        
        }
````

### 4、for of 循环 es6

```` javascript
        // 假设一个数组 
        let arr = [1,2,3]
        arr.a = '我是arr的私有属性'
        // for of循环(ES6) 只能遍历数组，且不会遍历出私有属性
        for(let val of arr){
            console.log(val)
        }
        // => 1  2  3
````

### 5、filter过滤器
遍历一个数组并返回一个新的数组，所以并不会影响原数组。遍历数组每一项，回调函数返回了true，就把这一项添加到新数组。其中回调函数有两个参数(item,index)，item是每一项，index是下标。index可以不写不用

```` javascript
        // 假设一个数组 
        let arr = [1,2,3]
        arr.a = '我是arr的私有属性'
        let newArr = []
        //filter过滤器 不会影响新数组
        //es6箭头函数
        newArr = arr.filter(item => item>2)    
        console.log(newArr) // [3]
        newArr = arr.filter(function(item){
            return item > 1
        })
        console.log(newArr) // [2,3]

````

### 6、map映射
map映射会去遍历数组的每一项，但是不会操作改变原数组，同filter一样会返回一个新的数组。回调函数返回的是什么，对应的新数组的那一项就会是什么

```` javascript
        // 假设一个数组 
        let arr = [1, 2, 3]
        arr.a = '我是arr的私有属性'
        let newArr = []
        // map映射
        newArr = arr.map(item => `<i>${item}</i>`)
        console.log(newArr)
        // console.log =>  ["<i>1</i>","<i>2</i>","<i>3</i>"]

````

### 7、include和find

```` javascript
        // 假设存在一个数组arr
        let arr = [1, 2, 3];
        //include 判断是否存在某值
        console.log(arr.includes(5));
        //include console =>  false
        console.log(arr.includes(2));
        //include console =>  true

        // find  返回通过校验规则的第一项
        let res = arr.find(function (item, index) {
            //如果这一项包含了5则返回true
            return item.toString().indexOf(2) > -1;
        })
        console.log(res);
        // find console  =>  2

````

### 8、some和every
some和every是两个作用截然相反的方法。some遍历数组，找到true，即返回true，否则返回false；every则是找到false返回false，否则返回true
```` javascript
        //假设存在一个数组arr
        let arr = [1,2,3];

        //some 只要有一个满足条件就返回 true
        let someResult = arr.some(item=>item > 2);
        console.log(someResult);
        //some console => true

        //every  所有选项都满足条件才返回true
        let evevyResult = arr.every(item=>item < 4);
        console.log(evevyResult);
        //every console => false

````

### 9、reduce  
（ie9一下不能用··）
收敛函数  四个参数  返回的是叠加后的结果  原数组不发生变化  回调函数返回  
`array.reduce(function(total, currentValue, currentIndex, arr), initialValue)`
> prev: 第一项的值或者上一次叠加的结果值  
  cur: 当前会参与叠加的项  
  index： 当前值的索引  
  arr: 数组本身
  


```` javascript
// 1、简单用法：做累加
var  arr = [1, 2, 3, 4, 5];
sum = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return prev + cur;
})
console.log(arr, sum);

// 2、计算某同学期末成绩
var result = [
    {
        subject: 'math',
        score: 88
    },
    {
        subject: 'chinese',
        score: 95
    },
    {
        subject: 'english',
        score: 80
    }
];
    // 利用for循环可以很简单得出结论
var sum = 0;
for(var i=0; i<result.length; i++) {
    sum += result[i].score;
}
    // reduce解决
var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, 0);

    // 第二个参数设置初始值，处初始类型
var sum = result.reduce(function(prev, cur) {
    return cur.score + prev;
}, -10);
    // 加权求和  假如该同学的总成绩中，各科所占的比重不同，分别为50%，30%，20%
var dis = {
    math: 0.5,
    chinese: 0.3,
    english: 0.2
}

var sum = result.reduce(function(prev, cur) {
    console.log(prev);
    return cur.score + prev;
}, -10);

var qsum = result.reduce(function(prev, cur) {
    return prev + cur.score * dis[cur.subject]
}, 0)

console.log(sum, qsum);

// 3、问如何知道一串字符串中每个字母出现的次数？
var arrString = 'abcdaabc';

arrString.split('').reduce(function(res, cur) {
    res[cur] ? res[cur] ++ : res[cur] = 1
    return res;
}, {})

// 4、可以将数组按照一定的规则转换成对象 / 或者其他转换
let Obj = [1, 2].reduce(function(res, cur,index) {
    res[index] = cur + 1
    return res;
},{})
console.log(Obj);

````