---
date:
title: 异步（JS）
tags: 
 - 前端
 - javascript
categories: 
 - javascript

cover: /image/cover11.jpg
---
## 1. 单线程和异步
JS简单介绍：
>1.JS是单线程语言，只能同时只做一件事
 2.浏览器和nodejs支持JS启动<font color="red">进程</font>,如Web Worker
 3.JS和DOM渲染共用一个线程，因为JS可修改DOM结构

异步的产生：
>**原因：**
 -- 遇到等待(网络请求，定时任务)不能卡住
 **形式：**
 使用回调函数(callback)

异步应用场景：
> -- 网络请求，如图片加载、ajax
  -- 定时任务

## 2. Event Loop
### 2.1 定义：
>-- 事件循环，是指浏览器或Node的一种解决javaScript单线程运行时不会阻塞的一种机制，也就是我们经常使用异步的原理

### 2.2 代码执行过程：
> -- 同步代码，一行一行放在`Call Stack`(调用栈)执行
  -- 遇到异步，会先“记录”下，等待时机（如定时器、网络请求等）
  -- 若`Call Stack`为空（即同步代码已全部执行完），触发event-loop
  -- 轮询查找`Callback Queue`(回调队列)，若有则移动到`Call Stack`中执行
  -- `Call Stack`为空后，又会触发event-loop
  -- 继续轮询查找(永动机)

**举例代码过程具体演示：**
{% tabs 代码执行过程 %}
<!-- tab 第一步 -->
1. 将`console.log('Hi')`推入调用栈执行
![](/image/textImg/event-loop1.png)
2. 调用栈执行后，控制台输出'Hi'
![](/image/textImg/event-loop2.png)
3. 执行完成后清空调用栈
![](/image/textImg/event-loop3.png)
<!-- endtab -->
<!-- tab 第二步 -->
1. 将函数推入调用栈
![](/image/textImg/event-loop4.png)
2. 执行函数将第一个参数`function cb1`其放入Web APIs中的定时器中
![](/image/textImg/event-loop5.png)
2. 清空调用栈
![](/image/textImg/event-loop6.png)
<!-- endtab -->
<!-- tab 第三步 -->
1. 将`console.log('Bye')`推入调用栈执行
![](/image/textImg/event-loop7.png)
2. 调用栈执行后，控制台输出'Bye'
![](/image/textImg/event-loop8.png)
3. 执行完成后清空调用栈
![](/image/textImg/event-loop9.png)
<!-- endtab -->
<!-- tab 第四步 -->
1. 同步代码执行完成，调用栈为空，事件循环启动，循环查看Callback Queue是否有需执行函数
2. 5秒后定时器将`cb1`这个函数放入回调队列里
![](/image/textImg/event-loop10.png)
3. 事件循环发现回调队列里有函数将其推入调用栈执行
![](/image/textImg/event-loop11.png)
4. 调用栈执行`console.log('cb1')`,浏览器打印
![](/image/textImg/event-loop12.png)
5. 先清空`console.log('cb1')`,再清空`cb1`函数
![](/image/textImg/event-loop13.png)
<!-- endtab -->
{% endtabs %}



## 3. Promise
### 3.1 Promise的状态
**三种状态(发生改变不可逆)：**
> <font color="blue">-- **pending(待定)**</font>      
  -- 不会触发`.then()`和`.catch()`
  <font color="blue">-- **fulfilled(已成功)** </font> 
  -- 操作成功，可触发后续的`.then()`
  <font color="blue">-- **rejected(已拒绝)** </font>  
  -- 操作失败，可触发后续的`.catch()`



### 3.2 Promise的创建和方法
1. 创建
-- `Promise`构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`
-- `resolve`将Promise对象的状态从 `pending` 变为 `resolved`，在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；
-- `reject`函数的作用是，将`Promise`对象的状态从`pending`变为`rejected`，在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去


```javascript
new Promise((resolve,reject) => {
    ...
    resolve()   //成功时返回
    reject()    //失败时返回
})
```
2. 方法
-- 成功时的方法：`.then((res) => { ... })`  
-- 失败时的方法：`.catch((err) => { ... })`  

3. 举例（加载图片）
```javascript
function loadImg(src) {
   return new Promise((resolve,reject) => {
       const img = document.createElement('img');
       img.onload = () => {
           resolve(img)
       }
       img.onerror = () => {
           reject(new Error(`图片加载失败${src}`))
       }
       img.src = src 
   })
}
let url1 = 'http://124.70.143.195/image/cover4.jpg';
let url2 = 'http://124.70.143.195/image22/cover10.jpg'
loadImg(url1).then((img1) => {
    console.log(img1.width)
    return loadImg(url2)
}).then((img2) => {
    console.log(img2.width)
}).catch((err) => {
    console.log(err)
})
```

### 3.3 .then()和.catch()对状态的改变
> then正常返回`fulfilled`的promise,若有报错则返回`rejected`
  catch正常返回`fulfilled`的promise,若有报错则返回`rejected`

```javascript
let p1 = Promise.resolve().then(() => {
    return 100
})
console.log(p1)     // 此时状态为fulfilled，打印pedding(因为promise异步)
let p2 = Promise.resolve().then(() => {
    throw new Error('error')
})
console.log(p2)     // 此时状态为rejected，打印pedding(因为promise异步)
```

**常见面试题：**
{% tabs 面试题 %}
<!-- tab 面试题1 -->
```javascript
Promise.resolve().then(() => {
    console.log(1)
}).catch(() => {
    console.log(2)
}).then(() => {
    console.log(3)
})
// 1，3
```
<!-- endtab -->
<!-- tab 面试题2 -->
```javascript
Promise.resolve().then(() => {
    console.log(1)
    throw new Error('error')
}).catch(() => {
    console.log(2)
}).then(() => {
    console.log(3)
})
// 1,2,3
```
<!-- endtab -->
<!-- tab 面试题3 -->
```javascript
Promise.resolve().then(() => {
    console.log(1)
    throw new Error('error')
}).catch(() => {
    console.log(2)
}).catch(() => {
    console.log(3)
})
// 1,2
```
<!-- endtab -->
<!-- tab 面试题4 -->
```javascript
Promise.reject().then(() => {
    console.log(1)
}).catch(() => {
    console.log(2)
}).catch(() => {
    console.log(3)
}).then(() => {
    console.log(4)
})
// 2,4
```
<!-- endtab -->
{% endtabs %}

### 3.4 Promise.all() .race()的用法
`Promsie.all()`
> -- 该方法用于将多个Promise实例，包装成一个新的Promise实例,等待所有promise完成返回数据
 `let p = Promise.all([p1,p2,p3...])`
 （1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
 （2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

`Promsie.race()`
>-- 该方法用于将多个Promise实例，包装成一个新的Promise实例，第一个promise状态改变则直接返回
 `let p = Promise.race([p1,p2,p3...])`
 只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数

### 3.5 手写Promise
**实现功能:**
> -- 初始化和异步调用
  -- then catch的链式调用
  -- API：.resolve .reject .race .all
```javascript
class MyPromise {
    constructor(fn) {
        const resolveHandler = (value) => {}
        const rejectHandler = (reason) => {}
        try {
            fn(resolveHandler,rejectHandler)
        } catch(err) {
            rejectHandler(err)
        }
    }
}
```

## 4. async和await
**async**:
-- `async`函数会返回一个Promsie,且Promsie对象的状态值为`fulfilled`
1. 如果你没有在`async`函数中写return，那么这个`async`函数返回的就是一个值为`undefined`的`fulfilled`状态的Promise 
2. 如果你写了return，那么return的值就会作为你`fulfiled`状态的 Promise 的值
```javascript
async function f1() {
    console.log(1)
}
let res = f1(1)
console.log(res)
/** 
Promise {<fulfilled>: undefined}
[[Prototype]]: Promise
[[PromiseState]]: "fulfilled"
[[PromiseResult]]: undefined
*/
async function f2() {
    return 1
}
let result = f2(1)
console.log(result)
/** 
Promise {<fulfilled>: 1}
[[Prototype]]: Promise
[[PromiseState]]: "fulfilled"
[[PromiseResult]]: 1
*/
```

**await**:
`await`右侧表达式结果可能存在两种情况：
<font color="blue">-- 不是Promise对象</font>
`await`会阻塞后面的代码，先执行`async`外的同步代码，再回到`async`内部，将这个非promise的结果，作为`await`表达式的结果
<font color="blue">-- 是Promise对象</font>
`await`会暂停async后面的代码，先执行`async`外的同步代码，再回到`async`内部，等着Promise对象`fulfilled`,然后把`resolve`的参数作为`await`表达式的运算结果

```javascript
function fn(){
    return new Promise(resolve=>{
        console.log(1)
        resolve()
    })
}
async function f1(){
    await fn()
    console.log(2)
}
f1()
console.log(3)
// 1  3  2
```

由于promise无fulfilled状态，所以await拿不到值，被阻塞，不打印2：
```javascript
function fn(){
    return new Promise(resolve=>{
        console.log(1)
    })
}
async function f1(){
    await fn()
    console.log(2)
}
f1()
console.log(3)
// 1  3  2
```

**异常捕获**:
`try { ... } catch() { ... }`

题目考察：
```javascript
async function async1() {
    console.log('async1 start');   // 2
    await async2();
    console.log('async1 end');    // 5
    await async3();
    console.log('async1 end again');  // 7
}
async function async2() {
    console.log('async2')         // 3
}
async function async3() {
    console.log('async3')        // 6
}
console.log('start');      //  1
async1()
console.log('end');        // 4
```

## 4. 宏任务(macroTask)和微任务(microTask)
### 4.1 异步任务的分类
>宏任务： setTimeout,setInterval,Ajax,DOM事件
微任务：Promise,async/await

### 4.2 event-loop和DOM渲染
>前情摘要：
 -- js是单线程的，而且和DOM渲染共用一个线程
 -- js执行时，需要留时机以供DOM渲染

**DOM渲染在代码中的时机：**
>--每次`Call Stack`清空，即同步任务执行完成，都是DOM重新渲染的机会
 --再去触发下一次event-loop

 ### 4.3 微任务和宏任务的区别
 >-- 宏任务：DOM渲染后触发，如setTimeout
  -- 微任务：DOM渲染前触发，如Promise

**原因：**
-- 微任务是ES6语法规定的
-- 宏任务是浏览器规定的
-- 所以在`Call Stack`清空后，要首先执行当前微任务，再DOM渲染，最后触发event-loop
![](/image/textImg/event-loop14.png)

**代码执行流程总结：**
1. 同步代码推入`Call Stack`执行
2. 遇到微任务将其放入microTask queue(微任务队列中)，遇到宏任务先放入Web APIs中
3. 同步任务执行完成后，即`Call Stack`清空，执行当前微任务
4. 尝试DOM渲染
5. 触发event-loop

面试题目：
```javascript
async function async1() {
    console.log('async1 start');
    await async2();
                                  
    console.log('async1 end');    
}
async function async2() {
    console.log('async2')        
}
console.log('script start');     
setTimeout(() => {               
    console.log('setTimeout')    
},0)
async1();
new Promise((resolve) => {
    console.log('promise1')     
    resolve()
}).then(() => {                 
    console.log('promsie2')     
})
console.log('script end')       
// 'script start' 'async1 start' 'async2' 'promise1' 'script end' 'async1 end' 'promise2' 'setTimeout'
```