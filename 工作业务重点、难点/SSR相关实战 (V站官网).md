
>SSR（Server-Side Rendering，服务器端渲染）和 SSG（Static Site Generation，静态站点生成）都是解决单页面应用（SPA）中 SEO 和首屏加载速度问题的方法。
>[极速加载还是绝佳SEO？探索CSR、SSR、SSG等渲染模式的优劣对决 - 掘金](https://juejin.cn/post/7233699680490799162)

##### 一、设计架构图

![[design.png]]

node 服务部署在服务器上（123 平台统一管理）

Nginx 代理层由 ias 统一管理，配置域名（v.qq.com）回包头头、重定向等 nginx 配置


#### 二、整体流程



##### Q&A

1. SSR和SSG的优劣，你的方案中SSR也开启了缓存，那为什么选用SSR而不是SSG？
	- 官网首页含有列表页这种不可枚举地址，数以万计，不可能都放在cdn缓存，如果都放在缓存中，如有发布需要同步更新各路径下的html时，这么多的文件带来的压力会比较大。(播放页很多cid，vid路径，也不可能都存在cdn)

2. 踩过什么坑
	- 同构问题，a标签嵌套，直出的html和浏览器解析出来的dom解构不一致
	- 同构问题，服务端数据，浏览器端数据处理不一致(ssr时没有过滤数组中的某个item，而浏览器hydration渲染时过滤了)





   

