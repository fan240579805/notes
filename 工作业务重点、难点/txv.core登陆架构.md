# **一、概述**


登陆组件包含在腾讯视频web端的核心库txv.core.js中，其中封装了登录组件、所有视频相关web共同复用的ui交互逻辑、上报逻辑。各种回调函数嵌套，非常恶心；重点在于登录，其余都是些耦合的屎山。接下来围绕重点剖析一下整体的视频web登录方案。￼整个腾讯视频的web登录主要围绕着cookie存放用户信息去实现，核心在于接入层域名video.qq.com和业务侧域v.qq.com下的cookie间的通信。
  

# **二、用户登录鉴权流程**

  

1. 用户打开登录框，内嵌一个iframe，拉起qq/wx提供的web互联登录页面。这个页面url会拼上一些参数，用于扫码或密码登陆成功后执行一些操作。￼如下地址，拉起web登录框会拼接并插入这个iframe到页面上￼[https://graph.qq.com/oauth2.0/show?redirect_uri=https%3A%2F%2Fvideo.qq.com%2Ftransfer_login_page%2Findex.html%3Fvplatform%3D2%26type%3Dqq%26appid%3D101483052&which=Login&display=pc&response_type=code&client_id=101483052](https://graph.qq.com/oauth2.0/show?redirect_uri=https%3A%2F%2Fvideo.qq.com%2Ftransfer_login_page%2Findex.html%3Fvplatform%3D2%26type%3Dqq%26appid%3D101483052&which=Login&display=pc&response_type=code&client_id=101483052)￼￼redirect_url，在互联平台登陆成功后重定向的地址，利用这种方式就可以在redirect_url中向我们腾讯视频自己的鉴权服务获取票据和身份信息了。
  
3. 扫码或密码登陆成功，互联平台会往redirect_url后面拼接一个query，appid=101483052&code=DF3C376085A09FE85EA864FD90860415，code是qq/wx侧登陆成功后下发的唯一标识，appid是视频注册的唯一平台id，登陆组件再拼接对应的平台好，登陆类型type=qq给登录中转页。￼此时iframe就会根据redirect_url去从定向到我们自己写好的中转页。￼￼redirect_uri: [https://video.qq.com/transfer_login_page/index.html?vplatform=2&type=qq&appid=101483052&code=DF3C376085A09FE85EA864FD90860415](https://video.qq.com/transfer_login_page/index.html?vplatform=2&type=qq&appid=101483052&code=DF3C376085A09FE85EA864FD90860415)

  
  

1. 在登录中转页 transfer_login_page 中，页面加载完成立刻拿着query参数向视频登录后台发起请求，视频鉴权后台向qq/wx侧验证code有效则通过，下发用户票据vusession和next_refresh_time下一次续期时间间隔、用户头像等信息。同时后台接口会将这些信息set-cookie设置到video.qq.com域下，回包data也会下发同样的字段信息**(****实际上有点多余，但有些历史项目需要接口直接****set-cookie)**
  
![Exported image](Exported%20image%2020240116155150-0.png)  
  
6. 中转页拿到用户信息、票据，通过postMessage通知业务方，业务方通过引入txv.core.js中的postMessage来接收票据并注入到业务方自己的域名下；￼保证video.qq.com域下的票据和业务方v.qq.com域下的票据一致。这样业务侧自己取cookie中的登录态信息、其他后台走统一接入层pbaccess.video.qq.com的服务请求携带的cookie完全一致，不会出错。￼
7. qq/wx登陆及视频鉴权完成，txv.core.js执行业务侧主动注册的addRedayCallback（登录态ready）事件，流程结束。
8. 续期，切换页面可见主动续期（十分钟冷却）、根据next_refresh_time设置一个定时器，这个值每次调用登录或者续期接口时都会更新，单位s，保证在下次某个时间点前一定发起续期。
9. 登出，调logout接口，接口会清理video.qq.com下的cookie中的登录信息，txv.core.js会清理业务侧v.qq.com域下的登录信息。

  
![Exported image](Exported%20image%2020240116155150-1.png)  
  
  
  
  

# **三、小程序码登录（已下线，但方案设计还不错，总结为自己的）**

  

**背景：**微信不提供公用的小程序码登录，但为了给小程序拉量，产品希望扫码登录优先走小程序，在小程序侧点击登陆后，通知到web端，实现小程序和web共同登录。

  

1. **整体架构**

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
![Exported image](Exported%20image%2020240116155150-2.png)

==注意：整个流程都围绕着登录状态status的变更：==

==const== ==R====EQ_INIT=========1== ==// 码成功生成==

==const== ==R====EQ_SCAN_SUCC=========2== ==//扫码成功==

==const== ==R====EQ_CANCEL_LOGIN=========3== ==//取消登陆==

==const== ==R====EQ_CONFIRM_LOGIN=========4== ==//确认登陆==

==const== ==R====EQ_REMOVE_STATUS=========5== ==//删除登录状态==

  

**2、先描述一下结构图中各个构成部分的主要功能**

==（这部分的各个结构的功能解析可能直接看起来会抽象些，可能直接看不太好理解，建议可以先看第三部分登录完整流程解析）==

**登录组件内嵌的微信登录页面**

==https://cache.tv.qq.com/miniapp/wxlogin/index.html?platform=2== ==这个页面会被内嵌到web端登录组件 还有mac/pc客户端的登录组件中。 主要功能是：==

==a.== ==与socket中台建立链接，（socket失败会用http轮询兜底）获取最新登录状态或key值==

==b.== ==与node中转后台通信==

==c.== ==生成小程序二维码 status=1== ==写入到====redis==

==d.== ==轮询触发node中台种植cookie到客户端、删除redis存储的登录状态操作 status=5==

==e.== ==轮询读====redis, key====被清理的的话二维码就失效了==

**websocket服务中台**

==根据node中转后台触发的push，推送最新的status等信息给登陆组件侧更新状态==  ==https://git.woa.com/tencentvideo_node/txv.alive==

**node中转后台**

==a====.== ==接收wx登录页面（status 1）和小程序端（status 2，status 3， status 4）同步来的状态。====￼====b====.== ==读写redis服务来查询或存储当前key对应的状态或登录状态信息；====￼====c====.== ==主动触发push操作 通知socket中台向web端推送最新状态====;==

==d====.== ==最后向浏览器端种植cookies时，拼接set-cookie http头并返回给浏览器。==

**redis端**

==同一个redis服务 提供读写两个接口，供node中台读取和存储登录流程状态和登录态信息等，小程序码生命周期内，这个====redis====存的====key====有效期为====5====分钟。====￼==因为登录特殊，为防止意外，在写入状态时，进行了双写，以视频侧redis为主，腾讯云redis为辅（反过来亦可），**当读取视频侧****redis****失败时，去读取腾讯云****redis****。**

**小程序端**

==微信扫码后拉起，拉起后读取到二维码链接上带来的唯一key。====￼====通知node中转后台扫码成功、取消登录、确认登录 这几个状态变更。====￼====当用户点击确认登录时和后台交互获取当前微信账号在腾讯视频的登陆态信息。==

**后台登录服务**

==根据小程序传来的wx账号信息，换算得到该账号在腾讯视频登录环境下的登陆态信息，返回给小程序端。==

  
  

**3、登录完整流程解析**

==接下来以一次完整的登录流程为例，讲述wx小程序登录流程到底按步骤依次经历了哪些过程：==

==1.首先登录组件的内嵌wx登录页面，会向websocket中台服务请求建立socket连接，socket连接建立后，socket中台会向wx登录页面推送一个唯一标志当前连接的key，这个key很重要，之后整个wx小程序登录流程都会围绕着这个key作为追溯等的唯一标记。==**一个****key****对应一个小程序码。**

==2.== ==登录组件拿到唯一key后会带上平台号等信息 拼接一个链接，形如：====￼====https://m.v.qq.com/author_login_tinyapp.html?appname=tinyapp&page=login%2Fwx%2Fwx&scene=bdd7ae425329dd8e_18====￼====再通过第三方组件用这个链接生成小程序的二维码。==

==3. 二维码生成成功后，登录组件会将二维码已经生成的状态status=1同步给node中转后台。==

==4. node中转后台收到来自登陆组件status变更为1的通知后，会将该状态存储到redis。==

==5. 当用户使用wx进行扫码，将会拉起小程序，小程序就拿到链接信息里面有key和平台信息等；小程序扫码成功后会主动向node中台发送请求，通知node中转后台当前已经扫码成功status状态变为2；==

==6. node中转后台此时会根据key 从redis中读取当前redis中存储的状态，这里有两种情况：====￼== ==1）如果读到redis存储的status=1,== ==那就认为此时的扫码成功是有效的，将会重新将用key作为键吧status=2存储在redis中，并触发websocket的push能力，将扫码成功这个状态变更通知到登录组件，登录组件的展示就会变更。== 

==2）如果读到redis存储的status大于或等于2,== ==那就说明这个二维码已经被别人扫过并走到后面的状态了。此时就不再往redis中更新状态了。==

==7. 用户扫码后小程序上将会有两个按钮：确认登录和取消登录；==

==8. 如果用户点击了取消登录，小程序会将取消登录的状态status=3发送http请求传递给node中转后台，中转后台也会读取一下当前key最新redis的状态，看这个状态变更是否有效，有效就更新到最新redis，并触发socket push能力，将状态同步到前端； 之后用户又可以重新扫码 开始新一轮的验证流程。==

==9. 如果用户在小程序点击了确认登录，此时小程序会和腾讯视频登录后台进行通信，用微信登录票据换取该账号对应腾讯视频的登陆态信息。再将登陆态信息同步到node中台同时status=4，此时小程序的使命就算完成了。==

==10. 接下来node中台会将登录态信息存储在redis中，存储的登陆态信息是这样的。==

=={====\===="errcode\"====:====0====,====\===="errmsg\"====:====\===="\"====,====\===="status\"====:====4====,====\===="extraData\"====:===={====\===="next_refresh_time\"====:====\===="6600\"====,====\===="nick\"====:====\===="陶明灯\"====,====\===="head\"====:====\===="https://thirdwx.qlogo.cn/mmopen/vi_32/Q0j4TwGTfTI7JkiaAQPeXbRdQhcy91qZQa8h9EVF0TIUAqCKsNFaPwic0wvUesLY0ibCmjhIXTjNibF8LYbPVnh0zg/132\"====}}==

==extraData中的就是后面要种植在页面中的cookie键值对。同时node中台会触发socket push将wx小程序扫码成功status=4 通知到登录组件端。==

==11. 登录组件知道当前小程序已经授权登录成功后，会主动往node中转后台发起http请求（====sync_status====），带的状态status=5，目的是删除前面流程中存储的status状态，还有一个最最重要的目的，触发后续流程中cookie的种植。==

==12. node中转后台接受到来自登录组件status=5的请求后，会去触发单独写redis的操作，将之前这个key的redis存储信息删除。但在这之前最最重要的操作是，它会先读取当前key在redis前面存储的信息，正常情况下 前一个状态是4 这时redis里面存储着小程序侧传递过来的登陆态相关信息，node中台将这些信息读取出来，根据里面的cookie过期时间键值对信息等，拼接生成set-cookie的http头信息返回到客户端，这样cookie信息就被种植到浏览器的video.qq.com下了。== 

==到此微信小程序登录的全部流程就结束了。==

  

|   |   |   |
|---|---|---|
|代码仓库￼|生成小程序码的内嵌iframe页面：miniapp_login|node中转层：http-cgi ￼1221|

  
  

# **四、****Q&A**

  

1. cookie安全性问题，cookie被截取了怎么办，
2. 为什么这个方案而不选用oAuth
3. cookie放在两个域下的必要性？
4. reids相关问题，
5. websocket如何接受并转发node中台的消息给web
  

  

![Exported image](Exported%20image%2020240116155150-3.png)