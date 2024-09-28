
>SSR（Server-Side Rendering，服务器端渲染）和 SSG（Static Site Generation，静态站点生成）都是解决单页面应用（SPA）中 SEO 和首屏加载速度问题的方法。
>[极速加载还是绝佳SEO？探索CSR、SSR、SSG等渲染模式的优劣对决 - 掘金](https://juejin.cn/post/7233699680490799162)

#### 两个重要的概念

##### 脱水（dehydrate）
将组件树序列化成静态的 HTML 片段，能直接看到初始视图，不过已经无法与之交互了，但这种便携的形态尤其适合网络传输。这个脱去动态数据，成为风干标本一样的静态快照的过程被称为脱水（dehydrate）。

##### 注水（hydrate）
与脱水相反，将这个 html 躯干复活为 Vue 应用的过程称为注水。客户端并不重新生成 HTML 组件，而是重用服务器发送给它的 HTML，并附加「数据」与「交互性」，构建成完整的 Vue 应用，这个过程被称为注水（hydrate）。

> Hydration is a process where a frontend framework like React, VueJS re-uses the static HTML structure it receives from the server (that was created at server-side at build time), and instead of re-generating the HTML nodes on the browser, simply “breathes” event handlers and interactivity into it.

![](../pictures/Pasted%20image%2020240929001017.png)

##### viteSSR 的一些关键流程
renderToString 函数执行时，vue 组件的 created 和 onServerPrefetch 等生命周期或钩子会在服务端渲染执行，将数据塞到 pinia Store 树上。
**renderToString 后才能拿到 pinia 的实例**，将服务端请求的数据取出来再序列化，拼接到 html 文档中，做到上图的 dehydrate 阶段。
#### 一、设计架构图

![[../pictures/design.png]]

node 服务：部署在服务器上（123 平台统一管理）

代理层：由 ias 统一管理，配置域名（v.qq.com）回包头头、重定向等 nginx 配置

缓存层：分为 redis 缓存和本地内存缓存，可以按需开启

onServerPrefetch 中请求页面片、服务端首屏需要的数据

定时备份服务：本地存一份未注入 store 数据的 index.html，当服务端渲染超时或直出报错时直接返回该 html 给浏览器，进行 csr 兜底? （todo：暂时没在 vite-app2 看到定时逻辑，下次再看看）
#### 二、vite-app2

核心步骤，脱水和

##### Q&A

1. SSR和SSG的优劣，你的方案中SSR也开启了缓存，那为什么选用SSR而不是SSG？
	- 官网首页含有列表页这种不可枚举地址，数以万计，不可能都放在cdn缓存，如果都放在缓存中，如有发布需要同步更新各路径下的html时，这么多的文件带来的压力会比较大。(播放页很多cid，vid路径，也不可能都存在cdn)

2. 踩过什么坑
	- 同构问题，a标签嵌套，直出的html和浏览器解析出来的dom解构不一致
	- 同构问题，服务端数据，浏览器端数据处理不一致(ssr时没有过滤数组中的某个item，而浏览器hydration渲染时过滤了)
	- ```Date.now()``` 服务端和浏览器端不能同时使用，因为服务器时间和用户电脑时间不一致，会出同构问题






   

