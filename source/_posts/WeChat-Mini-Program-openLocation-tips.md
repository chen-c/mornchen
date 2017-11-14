---
title: 微信小程序wx.openLocation无效(iOS)
date: 2017/05/04 06:00:39
tags: 
- 微信
- 开发
categories: 
- 踩坑
---
微信小程序 *wx.openLocation* 位置api应该是一个lbs中非常常用的一个api，使用微信内置地图查看位置，经常与wx.getLocation接口一起使用，将用户定位到地图中心点。
然而会发现有时候在iOS上会失效，到不了指定经纬度，安卓以及开发工具中都正常。

解决办法：尊重一下文档 [wx.openlocationobject](https://mp.weixin.qq.com/debug/wxadoc/dev/api/location.html#wxopenlocationobject)

会发现输入参数的经纬度类型是float，而iOS下似乎并没有对输入项做转换，因此需要自己做一下转换。

request里的经纬度做如下处理即可：

lat = parseFloat(lat);

PS：所以，要尊重下文档对吧，如支付宝般：）



