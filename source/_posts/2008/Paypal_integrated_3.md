---
title: Paypal国际版网站集成简易教程（三）：IPN的使用
date: 2008-07-17 20:10:00
tags: 
- Paypal
- 踩坑
- 时光旅行
categories: 
- 开发
---
![Paypal](https://v.moring.pw/mchen/img/2008/7-140504095212.jpg)
>什么是即时付款通知IPN
>当您收到新的付款交易或者已发生的付款交易的状态发生变化时，PayPal都将异步（即不作为网站付款流程的一部分） 发送付款详细数据到您所指定的URL，以便您了解买家付款的具体情况并做出相应的响应。这个过程我们称作即时付款通知（简称 IPN）。

<!-- more -->

最近事情比较多，一直没有继续更新，不好意思了，今天难得有空，就把最重要的一段先发上来了。
相信大部分网站集成Paypal都希望能够实现客户支付完成后返回信息到自己的网站，然后处理这些信息。比如客户在你的网站上购买了一个产品，通过Paypal完成了支付，接着Paypal告诉你的网站客户完成了支付以及支付信息，最后你的网站将这些信息记录到你自己的数据库中，并且将你的客户订单状态设为已支付，接着你就可以进行一系列的相关订单的后续操作。
IPN就能帮助我们实现这个功能，IPN示意图如下（来自Paypal.com）：

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_0.jpg)

当客户完成支付，Paypal会在后台通过post方式向你的服务器传送交易数据，来实现网站集成的功能。
接下来我们就来看IPN的实现方法。
第一步，你需要一个sandbox的帐号，这很重要，因为它能让你随心所欲的进行测试，而不用担心资金在天上飞。注册地址：https://developer.paypal.com 
第二步，登陆sandbox，新建一个商家帐号（卖方）和一个客户账号（买方），其中卖方帐号必须是Premier或者Business，不然无法使用IPN功能。

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_01.jpg)

新建买家帐号的时候记得在Account Balance中加上金额，不然你就没钱买东西了，如果paypal.com的帐号也能这样加钱多好。
新建完两个帐号：

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_00.jpg)
      
卖家帐号的test mode要设为enabled。
选中business帐号，点击下面的Enter Sandbox Test Site进入sandbox Test Site，登录，就像普通Paypal帐号的管理页面一样。

点击Profile，在Selling Preferences中选择Instant Payment Notification Preferences，点击edit加入IPN信息返回的地址，记得勾上前面那个选项，我是用.net写的接受文件，所以我的IPN地址是http://www.chenchen.org/ipn.aspx ,地址只要能在互联网上访问到就可以了。

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_02.jpg)

在Profile -> Selling Preferences ->Website Payment Preferences选项中，有一个Auto Return的选项，将它设为on，并且在下面的return地址中填入你希望的客户支付完成后返回的地址：
以上就完成了商家端的设置。

接下来是你网站上ipn.aspx文件的编写，这个是用来接收支付数据的，是非常关键的一个文件。
文件环境，.net 2.0，语言C#。
Ipn.aspx文件：此文件不用修改任何东西，代码都在cs文件中。
Ipn.aspx.cs文件：关键代码
定义一个VerifyIPN()函数：

``` csharp
bool VerifyIPN()
    {
        string strFormValues = Request.Form.ToString();
        string strNewValue;
        string strResponse;
        string serverURL = "https://www.sandbox.paypal.com/cgi-bin/webscr";

        HttpWebRequest req = (HttpWebRequest)WebRequest.Create(serverURL);
        req.Method = "POST";
        req.ContentType = "application/x-www-form-urlencoded";
        strNewValue = strFormValues + "&cmd;=_notify-validate";
        req.ContentLength = strNewValue.Length;

        StreamWriter stOut = new StreamWriter(req.GetRequestStream(), System.Text.Encoding.ASCII);
        stOut.Write(strNewValue);
        stOut.Close();

        StreamReader stIn = new StreamReader(req.GetResponse().GetResponseStream());
        strResponse = stIn.ReadToEnd();
        stIn.Close();

        return strResponse == "VERIFIED";
}
```
这段代码的作用是判断IPN信息是否来自Pyapal，如果不进行判断，那么恶意用户完全可以模拟一个信息post到你的网站上，让你认为订单已经完成支付，所以，必须首先对接受到的信息进行验证。
代码的基本原理，serverURL定义了验证地址，sandbox为：https://www.sandbox.paypal.com/cgi-bin/webscr，Paypal.com就是https://www.paypal.com/cgi-bin/webscr。
将paypal发送过来的所有信息加上一个&cmd;=_notify-validate参数，表示对这个信息进行验证，全部发送回paypal验证，如果信息确实存在，则返回VERIFIED字符串。
验证成功后，就可以用如下形式获得交易信息：

``` csharp
public partial class paypal_ipn : System.Web.UI.Page
{
    protected void Page_Load(object sender, EventArgs e)
    {
        if (VerifyIPN())//验证成功
        {
            string ppTx = Request.Form["txn_id"].ToString();//获取post中的各项变量值
                …
            执行将数据写入数据库等操作
        }   
    }

    bool VerifyIPN()
{
        具体代码见上。
    }
}
```
这样，就实现了将支付信息传回网站的功能。注：当交易状态发生改变时，paypal也会返回一个ipn，比如完成支付，退款等等。

介绍一下sandbox中IPN测试工具，登陆sandbox主帐号，就是你在sandbox上注册的那个，不是那些卖家/买家帐号。选择Test Tools -> Instant Payment Notification (IPN) Simulator：输入你的ipn接受文件地址，选择ipn信息的方式，然后会让你填具体信息内容，再点击Send IPN，就能模拟一个ipn到你网站的页面，可以用来测试IPN是否正常工作。

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_03.jpg)

不过这个工具有个小BUG，就是发送的IPN里不包括contact_phone这个变量，但是实际的IPN里是有的，这里要注意一下。
注：可以在商家帐号的Profile -> Selling Preferences ->Website Payment Preferences中，选择Contact Telephone Number这一栏，来确定是否需要发送买家联系电话。

![Paypal](https://v.moring.pw/mchen/img/2008/paypal_04.jpg)

>**此文来自时光旅行，内容已不维护:) **
