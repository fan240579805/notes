
### 一、**背景**

**txv.core 年代久远，最早的提交痕迹可追溯到10年前，存在如下几个问题：**

- login相关逻辑充斥着定时器和回调地狱、没有正确使用Promise处理异步，代码难读且易出问题。
- 登录逻辑和其他许多页面dom相关逻辑都在一个仓库中，耦合度高。
- 技术栈较为老旧且很多废弃的模块和上报代码以及api，历史包袱重大，不好维护。
- 日志系统不够完善。

v站（播放页、频道页、个人中心、直播页、搜索页、车载版、创作中心、）
外部网站（cf官网、qq炫舞官网）

#####  主要功能模块

- 不再使用的老旧模块

> 老上报 （~~heart_report~~, boss_report**[内部有bucket_id种植操作，待确认]**, ~~boss_pecker, recomm_report, irt~~）
>
> 无用的dom相关逻辑（ ~~nav/getVcoin, nav/upload, nav/playlist~~, ~~avatar/achIcon, avatar/money, avatar/unreadTips, avatar/userMessage, avator/utils~~）
>
> 低端浏览器弹窗 （ie_tips, browser-tips）**done**
>
> 懒加载图片（lazy_load） **done**
>
> 调试日志（log）**done**
>
> 老历史记录（history）**done**
>
> 无用utils （ live.lazyreport , ~~live.report, getPosition, onViewHelper~~）

- 继续保留的模块

> **登录（login）**
>
> **页头相关（nav, searchbox, cc-register, render, games）**
>
> 车载相关（lite）
>
> vip请求 （vip）
>
> 可能还在使用的老pv上报（pv_report/mypv）

 ***车载相关逻辑后续交由客厅团队负责，老pv上报后续询问ds看是否直接下掉。***



### 二、方案

#### 1. 总体思路

优先删除过时老旧代码，确保没有问题后开始着手重写核心login模块，暴露出完善的获取登录态、vip信息等相关方法给页面片和业务方使用；页头相关页面交互逻辑慢慢剥离 txv.core，交由页面片自行实现。

> ***本期优先实现登录态相关逻辑重写，其余页头交互逻辑先维持原样***

#### 2. login组件需求拆解

**(1)  请求方式更新，接口更新。**

登录，续期，登出等关键请求都是发起jsonp进行，存在安全隐患，且无法很好地抛出网络相关错误。首先和后台确定是否有新的，更稳定的接口可以替换，其次将老旧的请求方式升级为更安全，完善的方法。

**(2)  跨域通信方式变更。**

跨域同步cookie仍然使用的是修改domain的方式，该方式未来将被chrome废弃，需要替换为更安全的跨域同步方式如 postMessage。

**(3)  重写异步处理和发布订阅相关逻辑。**

目前异步处理回调嵌套太深，异步相关请求的状态容易带来gtk校验失败和vip信息查询失败等问题，且难以维护和排查，应当换为Promise合理处理异步逻辑。

**(4)  完善并发处理。**

txv.login对于同时打开多个tab页面的情况没有进行完善的加锁处理，容易造成同一时间点并发请求续期。

**(5)  新增监控告警和实时数据统计。**

完善上报和实时观测登录率、续期成功率、登录失败率等核心指标，补全缺失的实时监控能力。

#### 3. 实现

**技术选型：**ts + vite

在保证所有暴露给外部的api不变的前提下，进行login主要逻辑的重写。**主要拆分为以下几个类：**

**LoginManager类**

核心逻辑，续期，登录登出以及多tab页面时的控制逻辑。保留旧的方法和对外暴露的事件监听，使用post/get请求替换掉jsonp。将vip会员相关信息页集成到登录态信息的一部分。

**LoginUi类**

绘制登录框，登录错误提示等ui和基本交互逻辑，由LoginManager进行控制并暴露出对应的方法给外部调用拉起登录框。

**Cookie同步类**

用于将.video.qq.com域名下的登录相关cookie同步到 .v.qq.com 下。初始化video.qq.com下的iframe并将domain跨域通信方法改为postMessage，优化iframe的加载时机以及重试逻辑。

##### 3.1 新增对外暴露的api

> 结合业务，目前login可用的方法不足以满足一些特定场景的使用。

**（1）无需等待cookie同步直接续期的方法**

不用等待iframe直接调用续期的方法。目前txv.login中的首次续期必须等待内嵌video.qq.com的iframe加载完成才会发起请求，对于播放页、直播页等直接调用video.qq.com接口播放鉴权的场景下会影响起播好使，降低用户体验，因此需要对外暴露一个无需等待iframe加载同步cookie的方法。

```js
export interface refreshResponse {
  vusession: string;
  access_token: string;
  vuid: string;
  ...
};
// 不等待iframe加载直接续期, 传参决定是否重试, 得到续期结果回包
const directAuthRefresh: (retryNum: number = 0) => Promise<refreshResponse>;
```

**（2）访问video.qq.com下cookie的方法**

抛弃掉 domain = qq.com 的跨域通信方式后，要封装好相应的方法利用postMessage来满足业务侧需要直接访问video.qq.com下cookie的需求（例如种入guid传给后台）。

```js
// 读video.qq.com下的cookie
const getCookieFromIframe: (name: string) => string;
// 变更video.qq.com下的cookie
const setCookieToIframe: (key: string, value: string) => void;
```

##### 3.2 跨多tab页实现登录态的互相通信

> 目前的txv.core.js登录态，登陆成功后只能在当前页面生效。如果用户打开多个腾讯视频页面挂在后台，在其中一个页面登录腾讯视频时，其他的页面无法感知到用户登陆成功的信息（头像，vip角标不会更新），易使用户产生误解。

**（1）切换页面可见时，及时查询cookie中关键的登录态相关字段并更新到页面上，**

视频的登录票据依赖于cookie，登陆成功、续期成功、登出成功后组件都会往 ```v.qq.com ```和```video.qq.com```域名下更新登录票据相关的cookie，因此可以采用这种方式来实现后台tab页面感知到其他页面的登陆 / 登出成功。

```js
document.addEventListener(visibilityChange, function() {
  // 切换页面可见在执行查询操作
  if (!document[hidden]) {
    var vsiteIsLogin = txv.login.isLogin();
    if(vsiteIsLogin) {
      // 查询登录态已成功登录，及时更新页面头像、vip相关信息
      var head = login.getAvatar() || login.config.defaultHead;
      var	nick = login.getNick() || '';
      txv.vip.getVipInfo();
      login.showOnline(nick, head, 0);
    } else {
      // 查询登录态已不存在，及时隐藏页面头像、vip相关信息
      login.showNotLogin();
    }
  }
}, false);
```

**（2）跨域通知后台其他页面登录态改变**

现有的txv.core.js含有的登录态改变事件只能监听本页面的登录态，无法实现所有挂在后台的腾讯视频相关页面也感知到登录态的变化。

为了满足业务需求，有时候只是单纯地更新视图上的登录相关展示是不够的，需要利用postMessage暴露出去一个事件给业务方监听，满足业务有实时监听登录态变化操作业务逻辑的需求。

##### 3.3 部署

> 分为两种，txv.core.js的部署和video.qq域下的cookie.html的部署

```txv.core.js```

新增版本控制，无极表配置版本号，v站播放页频道页中间件请求wuji引入版本号并进行替换。当出现问题时及时通过修改版本号刷新cdn缓存。

```cookie.html```

同样新增版本号控制，延长max-age缓存时间，同时开启gzip压缩加快iframe加载速度，降低加载失败率。

##### 3.3 单测

引入jest框架补齐缺失的单元测试，增强代码的健壮性及后续可维护性。

### 三、注意事项

- 重构时可能需要分步进行，优先login逻辑，nav逻辑保留，需要解决commonJs和ESModule共存时的一系列问题。
- 老旧逻辑嵌套较深，需要彻底通读所有逻辑，兼容所有暴露出去的api，保证重构之后的代码稳定不出问题。
- 完善文档，提供完备的api介绍和日志排查文档，提供清晰的日志查询方法、完整的登录链路介绍。



