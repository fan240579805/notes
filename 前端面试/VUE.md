1. 写 React / Vue 项目时为什么要在列表组件中写 key,其作用是什么？

我们的 diff 操作可以更准确、更快速。

key 是给每一个 vnode 的唯一 id,可以依靠 key,更准确,更快的拿到 oldVnode 中对 应的 vnode 节点

  

[https://blog.csdn.net/qq_39414417/article/details/104763824](https://blog.csdn.net/qq_39414417/article/details/104763824)

  
  

1. ==Vuex== ==中====Mut====ation 必须是同步函数====，为什么？==

==因为在== ==Mutation== ==的每一条记录都要被====devtools====记录下来，以便追踪状态====state====的变更，如果有异步函数，我们不知道这个异步回调函数什么时候被回调。==

==有可能someMutation 已经执行了并被记录下来，但是它里面的异步函数还没执行完成，这会导致回调函数中进行的状态变更不能被追踪。==

==异步操作和复杂的逻辑操作应该交由====actons====。等到====action====中的异步函数执行完成，如====promise resolve====（），这时由====actions====提交给====mutation====，就能准确== ==的====追踪异步函数中的状态变更。==

  
6. ==为什么====vue== ==中====data====是以函数的形式返回==

```
  
data
() {
```

```
    
return
 {
```

```
      
id:
 
null
,
```

```
      
playing:
 
true
, 
// 
```

播放状态

```
      
showPlayList:
 
false
,
```

```
      
flag:
 
true
, 
// 
```

控制组件样式

```
      
childData:
 
true
,
```

```
      
// 
```

传给子组件的评论信息

```
      
allLits:
 [],
```

```
      
hotlist:
 [],
```

```
      
total:
 
0
,
```

```
    
};
```

```
  
},
```

因为

```
vue
```

中存在组件复用，一个组件可能被多个页面

```
/
```

组件调用，而

```
data
```

是一个对象，是个引用类型，如果直接 data：{…}的形式，==那么每个组件的====data====都是指向同一对象地址，其他重用的组件中的==

```
data
```

==会同时被修改==；而使用返回对象的函数，由于每次返回的都是一个新对（

```
Object
```

的实例），引用地址不同，则不会出现这个问题。

magnet:?xt=urn:btih:A03B412C41A37637A0004A18F917AB91A0A064B3&x._t-v1=372002514058361281

> 来自 <[https://www.jianshu.com/p/bc04ade2a552](https://www.jianshu.com/p/bc04ade2a552)>  
  
24. Prop默认值为什么要为工厂函数的形式返回？

```
  
props:
 {
```

```
    
artists:
 {
```

```
      
type:
 
Array
,
```

```
      
default
() {
```

```
        
return
 [];
```

```
      },
```

```
    },
```

```
    
alias:
 {
```

```
      
type:
 
Object
,
```

```
      
default
() {
```

```
        
return
 {};
```

```
      },
```

```
    },
```

```
}

```

原因就是，这里需要工厂函数返回的是针对数组或者对象，因为这两个是引用类型，不用函数返回则在子组件中修改这个对象或者数组本身，将会影响到父组件的状态。原因同上。

  
  
42. 怎样理解 Vue 的单向数据流？

所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。

这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。

  
46. **Vue** **怎么用 vm.$set() 解决对象新增属性不能响应的问题 ？**

受现代 JavaScript 的限制 ，Vue 无法检测到对象属性的添加或删除。**由于 Vue 会在初始化实例时对属性执行 getter/setter 转化，所以属性必须在 data 对象上存在才能让 Vue 将它转换为响应式的。**

但是 Vue 提供了 Vue.set (object, propertyName, value) / vm.$set (object, propertyName, value) 来实现为对象添加响应式属性，那框架本身是如何实现的呢？

我们查看对应的 Vue 源码：vue/src/core/instance/index.js

==export== ==function== ==set== ==(target:== ==Array====<====any====> | Object, key: any, val: any): any {==

  

- 如果目标是数组，直接使用数组的 splice 方法触发响应式；
- 如果目标是对象，会先判读属性是否存在、对象是否是响应式，最终如果要对属性进行响应式处理，则是通过调用 defineReactive 方法进行响应式处理（ defineReactive 方法就是 Vue 在初始化对象时，给对象属性采用 Object.defineProperty 动态添加 getter 和 setter 的功能所调用的方法）

  
54. Computed 和 watch 的区别
  

Computed 是计算属性，它的值有缓存，只有当他依赖的属性/数据变化时他才会变，我们可以利用它缓存的特性，避免每次获取值都计算一次，而是计算好了，等待获取。并且只在需要的时候进行更新。

==computed是用于定义==**基于数据之上的数据**==。而watch是你想在==**某个数据变化时做一些事情**

watch，用于监听数据/属性的变化，我们可以实时监听数据的变化，可以根据这个变化请求数据，做一些复杂的异步操作。这是Computed做不到的

  
60. Jquery 和 vue/react 的区别
    
    - 数据与视图分离
    
    jquery的数据与视图没有分离,数据和视图是混在一起的，而vue框架对数据和视图进行了分离，解耦（开放封闭原则，对扩展开放对修改封闭）
    
    - 数据驱动视图
    
    通过数据去驱动视图的变化，只关心数据的变化，dom操作被封装
    
    - 从jquery到vue、react 或者说是到mvvm的转变，是一个思想的转变，是将原有的直接操作dom的思想转变到操作数据上去。
    - vue更关注与ViewModel层，它是通过双向数据绑定把View和Model层链接起来，通过操作数据完成对页面视图的渲染。
    - 而jq是使用选择器$选取DOM对象，对其进行赋值、取值、事件绑定等操作，其实和原生的HTML相比，区别只在于更方便的获取和操作DOM对象，而且vue的数据和界面是在一起的，依赖于DOM元素的值。
    - vue则是通过对象将数据的view完全分离开，对数据进行操作不再需要引用响应的DOM对象，可以说数据和View是分离的，他们通过Vue对象的vm实现相互绑定。
      
    
61. MVVM [https://www.jianshu.com/p/9f460e66ce01](https://www.jianshu.com/p/9f460e66ce01)
    
    1. Model–View–ViewModel （MVVM） 是一个软件架构设计模式
    
    **（1）View 层**
    
    View 是视图层，也就是用户界面。前端主要由 HTML 和 CSS 来构建 。
    
    **（2）Model 层**
    
    Model 是指数据模型，泛指后端进行的各种业务逻辑处理和数据操控，对于前端来说就是后端提供的 api 接口。
    
      
    
    **（3）ViewModel 层**
    
    ViewModel 是由前端开发人员组织生成和维护的视图数据层。
    
    在这一层，前端开发者对从后端获取的 Model 数据进行转换处理，做二次封装，以生成符合 View 层使用预期的视图数据模型。
    
    该层实现了双向数据绑定，开发者不用麻烦的操作dom，而是通过操作数据，
    
    更新数据视图就会自动得到相应更新
    
      
    
62. Vue-router ([https://www.jianshu.com/p/4295aec31302](https://www.jianshu.com/p/4295aec31302))
    
    1. hash ---- 利用URL中的hash（“#”）
    
    hash（“#”）符号的本来作用是加在URL中指示网页中的位置：
    
      
    
    [http://www.example.com/index.html#print](http://www.example.com/index.html#print)
    
    #符号本身以及它后面的字符称之为hash，可通过**window.location.hash属性**读取。它具有如下特点：
    
      
    7. 利用History interface在 HTML5中新增的方法, [详情点击](https://www.w3cplus.com/html5/html5-history-api.html)

那么，我们要选择用哪种方式呢？

在vue-router中，它提供mode参数来决定采用哪一种方式，选择流程如下:

- 默认hash
- history 注：如果浏览器不支持history新特性,则采用hash方式
- 如果不在浏览器环境则使用abstract（node环境下）

  

  
> 来自 <[https://www.jianshu.com/p/bc04ade2a552](https://www.jianshu.com/p/bc04ade2a552)>  
  
  
  
  
  
  

我们阅读以上源码可知，vm.$set 的实现原理是：

  
  
  
  
  
  

vue-router通过**hash**与**History interfac**e两种方式实现前端路由，更新视图但不重新请求页面”是前端路由原理的核心之一，目前在浏览器环境中这一功能的实现主要有两种方式

  
  

mode 参数：

  
  
![VueRouter 
hash 
abstract 
history 
'E *history API ](Exported%20image%2020240116155115-0.png)

history模式的一个问题

  

我们知道对于单页应用来讲，理想的使用场景是仅在进入应用时加载index.html，后续在的网络操作通过Ajax完成，不会根据URL重新请求页面，但是难免遇到特殊情况，比如用户直接在地址栏中输入并回车，浏览器重启重新加载应用等。

  

hash模式仅改变hash部分的内容，而hash部分是不会包含在HTTP请求中的：

  

[http://oursite.com/#/user/id](http://oursite.com/#/user/id) // 如重新请求只会发送http://oursite.com/

故在hash模式下遇到根据URL请求页面的情况不会有问题。

  

而history模式则会将URL修改得就和正常请求后端的URL一样

  

[http://oursite.com/user/id](http://oursite.com/user/id)

在此情况下重新向后端发送请求，如后端没有配置对应/user/id的路由处理，则会返回404错误。官方推荐的解决办法是在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。同时这么做以后，服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面。或者，如果是用 Node.js 作后台，可以使用服务端的路由来匹配 URL，当没有匹配到路由的时候返回 404，从而实现 fallback。

**vue路由hash模式和history模式实现原理分别是什么，他们的区别是什么？**

  

hash 模式：

  

#后面 hash 值的变化，不会导致浏览器向服务器发出请求，浏览器不发出请求，就不会刷新页面

  

通过监听 **hashchange** 事件可以知道 hash 发生了哪些变化，然后根据 hash 变化来实现更新页面部分内容的操作。

  

history 模式：

  

主要是 HTML5 标准发布的两个 API，pushState 和replaceState，这两个 API可以在改变url，但是不会发送请求。这样就可以监听 url 变化来实现更新页面部分内容的操作

  

**区别**

  

url 展示上，hash 模式有“#”，history 模式没有

  

刷新页面时，hash 模式可以正常加载到 hash 值对应的页面，而 history 没有处理的话，会返回 404，一般需要后端将所有页面都配置重定向到首页路由

  

兼容性，hash 可以支持低版本浏览器和 IE。

[https://segmentfault.com/q/1010000010340823](https://segmentfault.com/q/1010000010340823)

  
  

11. vue生命周期

Created 时 props => methods =>data => computed => watch;

  
  
  
![new Vue() 
data observer 
beforeCreate 
created 
renderüf, 
YES 
Init 
Events & Lifecycle 
Init 
injections & reactivity 
Has 
"el" option? 
YES 
Has 
"template" option? 
Compile template 
into 
render function * 
beforeMount 
mounted 
state 
NO 
vm.$r 
is 
Compile 
outerHTN 
as templai 
€1JEvu$ 
Create vm.$el 
and replace 
"el" with it 
domfijBG±EE 
Mounted 
when 
vm.$destroy() 
is called 
beforeDestroy 
when data 
changes 
Vil ](Exported%20image%2020240116155115-1.png)

**vue** **父子组件的生命周期顺序**

Vue父子组件生命周期钩子的执行顺序遵循：从外到内，然后再从内到外，不管嵌套几层深，也遵循这个规律。

**一、加载渲染过程**

父beforeCreate->父created->父beforeMount->子beforeCreate->子created->子beforeMount->子mounted->父mounted

**二、子组件更新过程**

父beforeUpdate->子beforeUpdate->子updated->父updated￼

**三、父组件更新过程**

父beforeUpdate->父updated￼

**四、销毁过程**

父beforeDestroy->子beforeDestroy->子destroyed->父destroyed

  
  

1. diff算法和虚拟dom

虚拟 dom 相当于在 js 和 真实 dom 中间加了一个缓存，利用 dom diff 算法避免了没有必要的 dom 操作，从而提高性能。

**用javaScript 对象结构表示 dom 树的结构**，然后这个树构建一个真正的 dom树，插到文档当中，当状态变更的时候，重新构造一棵新的对象树。然后用**新的树和旧的树**进行比较，记录两棵树差异把所记录的差异应用到步骤1所构建的真正的dom 树上，视图就更新了。

==虚拟 DOM 有效降低大面积真实 DOM 的重绘与排版，因为最终与真实 DOM 比较差异，可以只渲染局部====，只渲染改变部分==

  

h函数生成vnode虚拟节点，

diff算法比较时，只有同一个vnode**（标签名相同，****key****相同）才会进行精细化比较**，否则直接暴力删除，插入新的节点

同层vnode进行比较，**不会跨层比较。**即使是同一片vnode，但是层级变了，他也会直接暴力删除旧的，插入新的

  

patch函数让虚拟节点上树(渲染到页面上)，打补丁

[https://www.cnblogs.com/wind-lanyan/p/9061684.html](https://www.cnblogs.com/wind-lanyan/p/9061684.html)

  
13. nextTick

由于Vue DOM更新是异步执行的，即修改数据时，视图不会立即更新，而是会监听数据变化，并缓存在同一事件循环中，等同一数据循环中的所有数据变化完成之后，再统一进行视图更新。为了确保得到更新后的DOM，所以设置了 Vue.nextTick()方法。

其实就是利用promise把nextTick要执行的内容放到微任务队列，**微任务队列里的任务会在当前一次的事件循环（同步代码）结束后执行**，不支持promise的话就会换成setTimeout​

  
17. VUE3.0

==vue 在 3.0 版本上使用 Proxy 重构的原因==

1. ==Object.defineProperty() 不会监测到数组引用不变的操作(比如 push/pop 等);==
2. ==Object.defineProperty() 只能监测到对象的属性的改变, 即如果有深度嵌套的对象则需要再次给之绑定 Object.defineProperty();==

1. 可以劫持数组的改变;
2. defineProperty 是对属性的劫持, Proxy 是对对象的劫持;

[https://developer.aliyun.com/article/741755](https://developer.aliyun.com/article/741755)

  

21. vuex原理

**vuex的store是如何挂载注入到组件中呢？**

vuex是利用vue的mixin混入机制，在before==C==reate钩子前混入vuexInit方法，vuexInit方法实现了store注入vue组件实例，并注册了vuex store的引用属性$store。

  

**vuex的state和getters是如何映射到各个组件实例中响应式更新状态呢？**

Vuex的state状态是响应式，是借助vue的data是响应式，将state存入vue实例组件的data中；Vuex的getters则是借助vue的计算属性computed实现数据实时监听。

[https://www.imooc.com/article/291242/](https://www.imooc.com/article/291242/)

[https://segmentfault.com/a/1190000021717329](https://segmentfault.com/a/1190000021717329)

  
  
31. 双向绑定原理

[https://segmentfault.com/a/1190000006599500](https://segmentfault.com/a/1190000006599500)

  
  
  
  

==首先罗列 Object.defineProperty() 的缺点:==

==关于 Proxy 的优点==

  
  
  
  
  

1. 为何vue采用异步渲染

vue是组件级更新，组件内有数据变化时，该组件就会更新。例：this.a = 1、this.b=2(同一个watcher)

  

**（****1****）原因：**

如果不采用异步更新，那么每次更新数据都会对当前组件进行重新渲染。所以为了性能考虑，Vue 会在本轮数据更新后，再去异步更新视图。而不是每当有数据更新，就立即更新视图。

**（****2****）过程：**

Vue是异步执行dom更新的，一旦观察到数据变化，Vue就会开启一个队列，然后把在同一个事件循环 (event loop) 中 观察到数据变化的 watcher 推送进这个队列。

如果这个watcher被触发多次，只会被推送到队列一次。这种缓冲行为可以有效的去掉重复数据，避免不必要的计算和Dom操作。

而在下一个事件循环时，Vue会清空队列，并进行必要的DOM更新。

**（****3****）源码解析：**

  

数据变化时，通过notify通知watcher进行更新操作；

通过subs[i].update依次调用watcher的update（未更新视图）；

将watcher放到队列中，在queueWatcher会根据watcher的id进行去重（多个属性依赖一个watcher），如果队列中没有该watcher就会将该watcher添加到队列中（未更新视图）；

通过nextTick异步执行flushSchedulerQueue方法刷新watcher队列（更新视图）；

[https://blog.csdn.net/HiSen_CSDN/article/details/104911040](https://blog.csdn.net/HiSen_CSDN/article/details/104911040)

[https://blog.csdn.net/weixin_34242819/article/details/91364286](https://blog.csdn.net/weixin_34242819/article/details/91364286)