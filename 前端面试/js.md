1. For in吗，for of 区别

for...in 语句用于遍历数组或者对象的属性（对数组或者对象的属性进行循环操作）。

**for in得到对对象的key或数组,字符串的下标（不建议和数组使用），****map****，****set****不能用**

**for of和forEach一样,是直接得到值，****map****，****set****，等有迭代器的数据结构都可以使用**

**for of不能对象用**

  
7. 数据劫持or数据代理

**Object.defineProperty(obj,prop,desc****)** 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

9. ==Object.defineProperty()：它的作用是直接在一个对象上定义一个属性，或者去修改一个已经存在的属性。====obj====：表示需要定义属性的当前对象。====prop====：当前需要定义的属性名。== ==desc====：属性描述符，就是更精确的控制对象属性。==

  

```
desc{
```

```
   enumerable
```

**：**

```
false, // 
```

**默认为**

```
 false
```

**，不可枚举，表示这个**

```
key
```

**不能被**

```
for in 
```

**遍历到，也不能呗拷贝**

```
   writable
```

**：**

```
 false, // 
```

**默认为**

```
false, 
```

**不可写入**

```
, 
```

**不能被修改，为**

```
true
```

**时才可以被赋值修改写入**

```
   configurable: false,// 
```

**默认为**

```
false,   
```

**属性描述符不可被改变**

```
 
```

**，**

```
true
```

**时才可以删除该属性**

```
}

```

  
  
3. event loop事件循环机制

js是一门单线程语言，他的同步异步和多线程是通过event loop实现的

先把同步代码执行完，在执行异步代码

异步要基于回调函数实现(settimeout)

7. ==JavaS====cript中有两种异步任务:==
    
    - ==宏任务: script（整体代码）, setTimeout, setInterval, setImmediate, I/O, UI rendering====，====ajax====，====dom====事件==
    - ==微任务:== ==Promise====/async,== ==process.nextTick（Nodejs====）====, Object.observe, MutationObserver;==
    - ==宏任务是由宿主发起的，而微任务由====JavaScript====自身发起。==
    - ==微任务执行时机====早====于宏任务==
8. event loop 图
  
![foo() 
query0 
hello() 
on Load 
Wab 
DOM 
ajax 
setTimeout 
onClick 
'Cf3r 
rnr,-u ](Exported%20image%2020240116155110-0.jpeg)  

==如这段代码：==

```
    
console
.
log
(
"hi"
);
```

```
    
setTimeout
(
function cb( )
 {
```

```
        
console
.
log
(
"
```

回调

```
"
);
```

```
    }, 
1000
);
```

```
    
console
.
log
(
```

"bye"

```
);
```

```
    
document
.
getElementById
(
"btn"
).
addEventListener
(
'click'
,()
=>
{
```

```
        
console
.
log
(
"click"
);
```

1. ==将同步代码==
    
    ```
    console
    .
    log
    (
    "hi"
    ); 
    ```
    
    压入函数执行栈，然后执行，控制台上打印 hi，弹出
2. 将异步代码 setTimeout(function cb( ) {});压入函数执行栈，然后将回调函数 cb( )压入

web Apis中等待时机（1000ms） setTimeout出栈

4. ==将同步代码==
    
    ```
    console
    .
    log
    (
    "bye"
    ); 
    ```
    
    压入函数执行栈，然后执行，控制台上打印 bye，弹出
5. ```
    document
    .
    getElementById
    (
    "btn"
    ).
    addEventListener
    ```
    
    （）压入函数执行栈，后将
    
    ```
    click
    ```
    
    事件的回调函数 ( )=>{ console.log("click");} 压入web Apis中等待时机（等待什么时候被点击）
6. **所有同步代码执行完成，调用栈****+****空** **event loop****开始轮询** ，不断轮询查找任务队列，有任务（函数）就压入执行栈执行
7. 1000ms到了，时机已到，定时器中的回调函数cb( )进入任务队列, 被轮询到栈中，cb( )执行，控制台打印 "回调"
8. 用户点击btn按钮 ，回调函数（）=>{} 时机到了，进入任务队列，随后被event loop轮询到栈中，执行，打印"click"，弹出
9. 所有代码执行完毕。

  

  
  
  

```
    })

```

js是单线程的，且执行代码和渲染dom共用一个线程

渲染dom的时机是，当同步代码执行完成后 **函数执行栈为空时，****event loop****暂停，开始渲染****dom**节点，渲染完成，再次触发下一次event loop，轮询查找任务队列

  

1. 为什么微任务比宏任务快？

因为微任务在dom渲染前触发，宏任务在dom渲染后触发

微任务不会触发在webAPIs中等待时机，而是在micorTaskqueue 微任务队列中等待被event loop轮询

![call stack 
l. call stack 
3. DOM 
Event Loop 
4. ME E ntLoop 
micro task queue ](Exported%20image%2020240116155110-1.png)

```
    
new
 
Promise
((
resolve
, 
reject
) 
=>
 {
```

```
        
console
.
log
(
"123"
);
// 
```

同步代码

```
 1
```

```
        
resolve
();
```

```
    
}).
then
(() 
=>
 {
```

```
        
// 
```

微任务

```
        
console
.
log
(
"1234"
);
// 4
```

```
    
})
```

```
    
setTimeout
(() 
=>
 {
```

```
        
console
.
log
(
"setTimeout"
);
//6
```

```
    
},
 
0
);
```

```
    
async
 
function
 
async1
() {
```

```
        
console
.
log
(
"111"
);
//
```

同步代码

```
 2
```

```
        
await
 
async2
(); 
// 
```

同步代码

```
        
// await 
```

后面相当于

```
then()
```

的回调函数属于微任务

```
        
console
.
log
(
"333"
);
// 
```

微任务

```
 5
```

```
    
}
```

```
    
async1
();
```

```
    
async
 
function
 
async2
() {
```

```
        
console
.
log
(
"2222"
);
//
```

同步代码

```
 3
```

```
    
}

```

**一开始的同步代码其实也是宏任务**

![Exported image](Exported%20image%2020240116155110-2.png)  

- Promise、async/await
    
    - 首先，new Promise是同步的任务，会被放到主进程中去立即执行。而.then()函数是异步任务会放到异步队列中去，那什么时候放到异步队列中去呢？当你的promise状态结束的时候，就会立即放进异步队列中去了。
      
    - 带async关键字的函数会返回一个promise对象，如果里面没有await，执行起来等同于普通函数；如果没有await，async函数并没有很厉害是不是
      
    - await 关键字要在 async 关键字函数的内部，await 写在外面会报错；await如同他的语意，就是在等待，等待右侧的表达式完成。此时的await会让出线程，阻塞async内后续的代码，先去执行async外的代码。等外面的同步代码执行完毕，才会执行里面的后续代码。就算await的不是promise对象，是一个同步函数，也会等这样操作
  
  
  
- Symbol ES6新增的数据类型

本质上是一种唯一标识符，可用作对象的唯一属性名，这样其他人就不会改写或覆盖你设置的属性值。

声明方法：

  

```
let
 
id
 = 
Symbol
(
"id"
);
```

```
let
 
obj
 = {
```

```
    
[id]:
 
'symbol'
```

```
};
```

```
console
.
log
(
obj
);
```

```
S
ymbol 
```

数据类型的特点是唯一性，即使是用同一个变量生成的值也不相等。

  

```
 let id1 = Symbol('id');
```

```
 let id2 = Symbol('id');
```

```
 console.log(id1 == id2);  //false
```

```
Symbol 
```

数据类型的另一特点是隐藏性，

```
for···in
```

，

```
object.keys() 
```

不能访问

  

```
 let id = Symbol("id");
```

```
 let obj = {
```

```
  [id]:'symbol'
```

```
 };
```

```
 for(let option in obj){
```

```
     console.log(obj[option]); //
```

空

```
 }
```

但是也有能够访问的方法：

```
Object.getOwnPropertySymbols
```

```
Object.getOwnPropertySymbols 
```

方法会返回一个数组，成员是当前对象的所有用作属性名的

```
 Symbol 
```

值。

  
  
  
  
  
  
  
  
  
  

  

```
 let id = Symbol("id");
```

```
 let obj = {
```

```
  [id]:'symbol'
```

```
 };
```

```
let array = Object.getOwnPropertySymbols(obj);
```

```
 console.log(array); //[Symbol(id)]
```

```
 console.log(obj[array[0]]);  //'symbol'

```

9. **值类型(基本类型)**==：字符串（String）、数字(Number)、布尔(Boolean)、对空（Null）、未定义（Undefined）、Symbol。==

**引用数据类型**==：对象(Object)、数组(Array)、函数(Function)。==

  
  

1. 箭头函数与普通函数（function）的区别是什么？构 造函数（function）可以使用 new 生成实例，那么箭头函数可 以吗？为什么？

箭头函数是普通函数的简写，可以更优雅的定义一个函数，和普通函数相比，

有以下几点差异：

4. **函数体内的** **this 对象，就是定义时所在的对象，而不是使用时所在的对** **象。**
5. **不可以使用** **arguments 对象，该对象在函数体内不存在。如果要用，可** **以用** **rest 参数代替。**
6. 不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数。
7. 不可以使用 new 命令，因为：

（1）.没有自己的 this，无法调用 call，apply。

（2）.没有 prototype 属性 ，而 new 命令在执行时需要将构造函数 的 prototype 赋值给新的对象的 __proto__

10. js中的栈和堆

**一.栈和堆**

**栈(stack)**：栈会自动分配内存空间，会自动释放，存放**基本类型**，简单的数据段，占据固定大小的空间。

**基本类型**：String，Number，Boolean，Null，Undefined

**堆(heap)**:动态分配的内存，大小不定也不会自动释放，存放**引用类型**，指那些可能由多个值构成的对象，保存在堆内存中，包含引用类型的变量，实际上保存的不是变量本身，而是指向该对象的指针。

**引用类型**：Function，Array，Object

**二.区别**

**栈**：所有在方法中定义的变量都是放在栈内存中，随着方法的执行结束，这个方法的内存栈也自然销毁。

优点：存取速度比堆快，仅次于直接位于CPU中的寄存器，数据可以共享；

缺点：存在栈中的数据大小与生存期必须是确定的，缺乏灵活性。

**堆**：堆内存中的对象不会随方法的结束而销毁，即使方法结束后，这个对象还可能被另一个引用变量所引用(参数传递)。创建对象是为了反复利用，这个对象将被保存到运行时数据区。

==如何知道一个对象到底有没有被引用？不被引用的话就会被垃圾回收机制回收，那么如何知道呢？==

**标记****-****清除法：没被标记的就是没被引用的，直接回收**

23. js垃圾回收机制

[https://segmentfault.com/a/1190000018605776](https://segmentfault.com/a/1190000018605776)

内存泄漏的识别方法

怎样可以观察到内存泄漏呢？

[经验法则](https://www.toptal.com/nodejs/debugging-memory-leaks-node-js-applications)是，如果连续**五**次垃圾回收之后，内存占用一次比一次大，就有内存泄漏。这就要求实时查看内存占用

  
29. 设计模式

[https://juejin.cn/post/6844903607452581896](https://juejin.cn/post/6844903607452581896)

[https://segmentfault.com/a/1190000012547812](https://segmentfault.com/a/1190000012547812)

  
33. 计算首屏加载时间

==performance.timing.loadEventEnd==:返回当前网页 load 事件的回调函数运行结束时的 Unix 毫秒时间戳。如果该事件还没有发生,返回 0。

==performance.timing.navigationStart==:当前浏览器窗口的前一个网页关闭,发生 unload 事件时的 Unix 毫秒时间戳。如果没有前一个网页,则等于 fetchStart 属性。

  

var t = performance.timing;

var pageLoadTime = t.loadEventEnd - t.navigationStart;

==// 在这里pageLoadTime就是首屏时间==

  

**或则在谷歌浏览器****F12****的****network****中右下角查看** **DOMContentLoaded****，而这段时间就是****HTML****文档被加载和解析完成。**

**load****，页面上所有的资源（图片，音频，视频等）被加载以后才会触发****load****事件，简单来说，页面的****load****事件会在****DOMContentLoaded****被触发之后才触发。**

  
  
45. **为什么****js****是单线程的**

JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

  
48. **JavaScript 为什么要区分微任务和宏任务**

区分微任务和宏任务是为了将异步队列任务划分优先级，通俗的理解就是为了插队。

  

==js====再函数执行栈执行完==**为空时，会查询微任务，宏任务****2****个队列**==，实时查询，就算这时候正在执行宏任务队列中得任务，==**微任务队列中为空**==。只要==**突然**==来个微任务（比如突然插入一个====Promise====），事件循环检测到微任务队列新增了一项任务==**，就会优先执行微任务**==，实现插队（为紧急任务提供优先通道）的目的。==

  

由此可见，我们可以在下一次 Event Loop 之前进行插队。比如Promise新注册一个任务，就可以直接放在微任务队列中

  

如果不区分 Microtask 和 Macrotask，那就无法在下一次 Event Loop 之前进行插队，其中新注册的任务得等到下一个 Macrotask 完成之后才能进行，这中间可能你需要的状态就无法在下一个 Macrotask 中得到同步。

  
57. 严格模式

“use strict”;开启严格模式

==设立严格模式的原因：==

==- 消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;==

==- 消除代码运行的一些不安全之处，保证代码运行的安全；==

==- 提高编译器效率，增加运行速度；==

==- 为未来新版本的Javascript做好铺垫。==

- ==this====不可以指向全局====window====。====(====除非是箭头函数，箭头函数的====this====不可能被修改====)==
- ==函数中的====arguments====实参不能被修改==
- ==变量定义必须使用====var== ==不可以直接暗示全局变量== ==a=10;==

  
66. ==js====中的协程==

Generator

yiled

Async 和 await 原理就相当于他们两的语法糖，将异步代码给同步化（实际上js还是单线程的，只不过将异步回掉函数处理了）

[js](https://zhuanlan.zhihu.com/p/148462034)协程到底是什么

[深入 async 和 generator 原理](https://zhuanlan.zhihu.com/p/115112361)

  
73. 不同域名之间共享cookie和获取相应的localStorage方案

**localStorage跨域**

==可以使用==

```
postMessage
```

==和==

```
iframe
```

==思路如下：==

==假设有==

```
a.haorooms.com/text.html
```

==和==

```
b.haorooms.com/text.html
```

==两个页面。==

==通过==

```
a.haorooms.com/text.html
```

==页面去修改==

```
b.haorooms.com/text.html
```

==页面的本地数据：==

==①　在==

```
a.haorooms.com/text.html
```

==页面创建一个==

```
iframe
```

==，嵌入==

```
b.haorooms.com/text.html
```

==页面。==

==②==　

```
a.haorooms.com/text.html
```

==页面通过==

```
postMessage
```

==传递指定格式的消息给==

```
b.haorooms.com/text.html
```

==页面。==

==③==　

```
b.haorooms.com/text.html
```

==页面解析==

```
a.haorooms.com/text.html
```

==页面传递过来的消息内容，调用==

```
localStorage API 
```

==操作本地数据。==

==④==　

```
b.haorooms.com/text.html
```

==页面包装==

```
localStorage
```

==的操作结果，并通过==

```
postMessage
```

==传递给==

```
a.haorooms.com/text.html
```

==页面。==

==⑤==　

```
a.haorooms.com/text.html
```

==页面解析==

```
b.haorooms.com/text.html
```

==页面传递回来的消息内容，得到==

```
 localStorage 
```

==的操作结果。==

  
> 来自 <[https://www.cnblogs.com/vsmart/p/9388597.html](https://www.cnblogs.com/vsmart/p/9388597.html)>  
  
  
  
  
  
  
  
  

```
var
 
test111
 = (...
rest
) 
=>
 {
```

```
    
console
.
log
(
rest
);
```

```
}
```

```
test111
(
1
, 
2
, 
3
)











```

**开启严格模式后**

  
  

**cookie****：**

```
nginx
```

反向代理，如天猫和淘宝域名不同，可以选择

```
nginx
```

代理，给两个域名都分发相同的

```
cookie
```

```
jsonp
```

，通过

```
script
```

不受跨域限制，向服务端请求，服务端将相应的处理

```
cookie domain
```

的

```
js
```

代码

```
(
```

在各个不同域名重新种

```
cookie)
```

返回

[https://www.cnblogs.com/hujunzheng/p/5744755.html](https://www.cnblogs.com/hujunzheng/p/5744755.html)

  
  
> 来自 <[https://www.cnblogs.com/vsmart/p/9388597.html](https://www.cnblogs.com/vsmart/p/9388597.html)>