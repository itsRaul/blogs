---
title: H5的支付
date: 2018-10-01
tags: ['wx.chooseWXPay','Alipay']
categories: H5
---
### JSSDK微信支付、h5浏览器支付宝支付、微信h5支付
<!-- more -->

### 微信支付
#### 引入JS文件
```
http://res2.wx.qq.com/open/js/jweixin-1.4.0.js 
```
#### 通过config接口注入权限验证配置
使用JSSDK的页面必须先配置信息，否则无法使用。配置的信息通过后端接口返回，前端调用。
chooseWXPay:微信支付的接口
```
wx.config({
    debug: true, // 开启调试模式
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: ['chooseWXPay'] // 必填，需要使用的JS接口列表
});
```
#### 通过ready接口处理成功验证
config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
```
wx.ready(function(){
    wx.chooseWXPay({
        timestamp: , // 支付签名时间戳
        nonceStr: , // 支付签名随机串，不长于 32 位
        package:, // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*）
        signType: 'MD5', // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
        paySign: , // 支付签名
        success: function (res) {
            if(res.errMsg == "chooseWXPay:ok"){
                alert("支付成功")
            }else{
                alert(res.errMsg);
            }
        }
    });
});
```
1.坑：当支付成功时，前端看到支付成功，有个坑可能会出现，就是后端订单的支付信息可能会显示未支付状态
2.出现的原因：用户支付完成，直接退出，后端未接收到支付完成的回调
3.解决方法：后端提供支付订单状态接口，在支付成功时调用后端接口来确认订单支付状态

### 非微信浏览器的微信h5支付
```
wxpay.mweb_url,直接从后台返回的URL跳转唤起微信支付
window.location.href = wxpay.mweb_url
```
后端接口返回wxpay参数下面的mweb_url,前端跳转唤起微信支付即可，前端无需做任何处理
### 支付宝支付
1.关于在h5浏览器的支付宝支付，前端直接调用后端接口
2.后端提供接口alipay里有关于html页面，显示支付宝支付页面，继续支付和已完成支付，点击继续支付即可完成支付
3.坑：后端提供的接口是否可以跳转支付页面，需要前后端联调
前端处理
```
res => {    
    const div = document.createElement('div');
    div.innerHTML = res.html; //接口返回的html
    document.body.appendChild(div);
    document.forms.alipaysubmit.submit(); 
}
```
