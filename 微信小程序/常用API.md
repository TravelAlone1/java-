## 常用API

## 登录

- wx.login：调用接口，获取登录凭证（code）
- wx.checkSession：检查登录态是否过期

> wx.checkSession

通过 wx.login 接口获得的用户登录态拥有一定的时效性。用户越久未使用小程序，用户登录态越有可能失效。反之如果用户一直在使用小程序，则用户登录态一直保持有效。具体时效逻辑由微信维护，对开发者透明。开发者只需要调用 wx.checkSession 接口检测当前用户登录态是否有效。

登录态过期后开发者可以再调用 wx.login 获取新的用户登录态。调用成功说明当前 session_key 未过期，调用失败说明 session_key 已过期。



总结：**其实就是存在缓存中，配合 wx.getStorageSync 使用**

## 数据缓存

- wx.getStorageSync：获取小程序缓存中的值