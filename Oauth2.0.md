##OAuth2.0---Oltu

##请求流程
###Step1：获取Authorization Code，授权界面

- grant_type 授权类型authorization_code
- redirect_uri 回调路径
- scope 授权范围
- state 客户端状态值

返回的授权auth_code和原始状态值state

###Step2：通过Authorization Code获取Access Token

-  grant_type 请求授权类型access_token
-  client_id  用户授权成功之后回调redirect_uri 返回的auth_code
-  client_secret  分配给第三方应用的code
-  auth_code  请求授权时客户端请求过来的
-  redirect_uri

返回的是access_token和有效期expires_in和refresh_token刷新

###Step3：权限自动续期，refresh access_token

- grant_type 授权类型refresh_token
- client_id
- client_secret
- refresh_token

返回的新的access_token和新的refresh access_token

###Step4：使用Access Token来访问和修改数据

- access_token
- op 对应操作

---
**参考文档：**
[Oltu](http://oltu.apache.org/)
[OAuth2集成-开涛的博客](http://jinnianshilongnian.iteye.com/blog/2038646)

[理解OAuth 2.0 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
[oauth2.0 官网](http://oauth.net/2/)
[腾讯开放平台-OAuth2.0简介](http://wiki.open.qq.com/wiki/website/OAuth2.0%E7%AE%80%E4%BB%8B)
[新浪授权-OAuth2.0概述](http://open.weibo.com/wiki/%E6%8E%88%E6%9D%83%E6%9C%BA%E5%88%B6%E8%AF%B4%E6%98%8E)
[使用OAuth2.0访问豆瓣API](https://developers.douban.com/wiki/?title=oauth2)