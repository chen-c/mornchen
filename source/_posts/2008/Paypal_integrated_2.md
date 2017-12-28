---
title: Paypal国际版网站集成简易教程（二）：快速加入支付按钮
date: 2008-07-10 19:15:00
tags: 
- Paypal
- 踩坑
- 时光旅行
categories: 
- 开发
---
![Paypal](https://v.moring.pw/mchen/img/2008/7-140504095212.jpg)
本篇主要介绍如何在你的网站上快速加入paypal支付按钮，通过这个按钮，人们可以方便快速的付款到你的paypal帐户。
<!-- more -->
首先，你需要注册一个paypal帐户，帐户类型分为Personal（个人）、Premier（高级）和Business（商业），他们的差别对于开发者来说主要是返回的信息，Personal的不能使用IPN和PDT等商家工具，即不能获取交易信息，但是Personal帐户收款无需手续费；Premier和Business当然就提供了IPN和PDT功能，能够在客户支付成功后，将交易信息传给指定的网站，用来二次使用。Premier和Business使用上的差异我不是很清楚，应该是月收款额和手续费上的区别吧。

这里插进来介绍一下paypal sandbox，也就是沙盒，是paypal提供给开发者的一个工具，非常好用，你可以在https://developer.paypal.com/ 注册和使用它。登录以后可以新建帐户，设置余额和帐户类型，有一点要注意，每次使用时必须先登录sandbox才能使用新建的那些帐户。Sndbox里面有个测试工具，可以发送IPN的，以后会用到。

接着上面的内容，注册完帐户以后，当然，一开始开发最好使用sandbox，不然资金转来转去手续费都扣光了，paypal的费率如下：
[商家收费](https://www.paypal.com/c2/webapps/mpp/paypal-seller-fees?locale.x=zh_C2)
比起国内的支付工具，paypal贵了好多，当然，和国情也有关系。
接下来是按钮的代码，很简单，就是一个普通的网页表单代码：

``` xml
<form action="https://www.paypal.com/cgi-bin/webscr" method="post">
   <input type="hidden" name="cmd" value="_xclick">
   <input type="hidden" name="business" value="sample@sample.com">
   <input type="hidden" name="item_name" 
   value="Item Name Goes Here">
   <input type="hidden" name="item_number" 
   value="Item Number Goes Here">
   <input type="hidden" name="amount" value="100.00">
   <input type="hidden" name="no_shipping" value="2">
   <input type="hidden" name="no_note" value="1">
   <input type="hidden" name="currency_code" value="USD">
   <input type="hidden" name="bn" value="IC_Sample">
   <input type="image" src="https://www.paypal.com/
   en_US/i/btn/x-click-but23.gif" 
   name="submit" alt="Make payments with payPal - it's fast, 
   free and secure!">
   <img alt="" 
   src="https://www.paypal.com/en_US/i/scr/pixel.gif" 
   width="1" height="1">
</form>
``` 
如果使用sandbox，action地址改成https://www.sandbox.paypal.com/cgi-bin/webscr即可，上面这段还是很容易理解的，看下name和value基本上就能知道每个值的含义了。
保存用浏览器打开，就会看到一个paypal的按钮，点击过去按照提示操作，就能付款到business指定值的帐户了。
这就是一个最简单的paypal支付按钮。
再下一篇中会讲一下如何在付款成功之后，将信息返回到你的网站，大部分需求都是客户完成付款后返回信息到网站的数据库，记录网站客服的交易信息。
最后稍微了解下为什么海外用户喜欢用自己的网站做生意，而不是像国内一样用淘宝之类的C2C平台。一开始我也很不理解，有免费的平台为什么要自己建设网站还要支付paypal的手续费，后来和客户了解了一下，他们做的事网游虚拟交易，国外最大的C2C平台就是eBay，但只有德国可以售卖虚拟物品，并且eBay上的交易并不是免费的，收取的手续费远远高于paypal的费率，因此，更多的海外用户选择自己建设网站进行电子商务。


>**此文来自时光旅行，内容已不维护:) **