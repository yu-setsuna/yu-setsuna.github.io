---
title: CSRF
date: 2019-11-26 14:19:44
tags: 
    - web
    - csrf
categories: web
---


## 介绍 
CSRF（Cross-site request forgery），中文名称：跨站请求伪造，也被称为：one click attack/session riding，缩写为：CSRF/XSRF。 


## 完整流程 

0. 受害者登录a.com，并保留了登录凭证（Cookie）。 

1. 攻击者引诱受害者访问了b.com。 

2. b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会… 

3. a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。 

4. a.com以受害者的名义执行了act=xx。 

5. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。 


CSRF攻击是以用户身份发送恶意请求（到受信网站），用于发送邮件、购买商品、转账等。 

<!-- more -->

## 例子 

### Get请求 

``` html
<img src=http://www.mybank.com/Transfer.php?toBankId=11&money=1000>
```

### Post请求 
``` html
<form action="http://www.mybank.com/Transfer" method=POST> 
    <input type="hidden" name="account" value="xiaoming" /> 
    <input type="hidden" name="amount" value="10000" /> 
    <input type="hidden" name="for" value="hacker" /> 
</form> 
<script> document.forms[0].submit(); </script>  
```

## 思想 

在Csrf攻击中Get请求比Post请求更容易，应严格遵守HTTP规范，使用Post或Put更新请求资源。 

CSRF攻击是源于WEB的隐式身份验证机制！WEB的身份验证机制虽然可以保证一个请求是来自于某个用户的浏览器，但却无法保证该请求是用户批准发送的 


## 防御 

可从服务器端和客户端两方面进行，在服务器端防御效果更好 

### 1.Cookie Hashing（双重Cookie认证） 

``` php
<?php 
　　　　$hash = md5($_COOKIE['cookie']); 
　　?> 

　　<form method=”POST” action=”transfer.php”> 
　　　　<input type=”text” name=”toBankId”> 
　　　　<input type=”text” name=”money”> 
　　　　<input type=”hidden” name=”hash” value=”<?=$hash;?>”> 
　　　　<input type=”submit” name=”submit” value=”Submit”> 
　　</form> 
```

由于网站无法获取第三方Cookie，因此这个方案可以杜绝大部分Csrf攻击。 

但任何跨域请求都会导致前端无法获取Cookie中的字段（包括子域名之间），于是：

如果用户访问的网站为`www.a.com`，而后端的api域名为`api.a.com`。那么在`www.a.com`下，前端拿不到`api.a.com`的Cookie，也就无法完成双重Cookie认证。 

于是这个认证Cookie必须被种在a.com下，这样每个子域都可以访问。 

任何一个子域都可以修改a.com下的Cookie。 

某个子域名存在漏洞被XSS攻击（例如upload.a.com）。虽然这个子域下并没有什么值得窃取的信息。但攻击者修改了a.com下的Cookie。 

攻击者可以直接使用自己配置的Cookie，对XSS中招的用户再向`www.a.com`下，发起CSRF攻击。 


### 2.CSRF TOKEN 

用户打开页面时，服务器给该用户生成一个Token，并保存在Session中，页面加载时将Token插入`<a/><form/>`，如 
``` html
<input type=”hidden” name=”csrftoken” value=”tokenvalue”/> 
```
服务器对Token进行验证 

但分布式网站中，使用Session存储CSRF Token还需要Redis等进行。在读取和验证CSRF Token时会可能引起较大的性能问题， 

可采用Encrypted Token Pattern方式的计算结果代替随机字符串，这样可以避免读取Token 


> Token是一个比较有效的CSRF防护方法，只要页面没有XSS漏洞泄露Token，那么接口的CSRF攻击就无法成功。  
> 来自 [https://juejin.im/post/5bc009996fb9a05d0a055192](https://juejin.im/post/5bc009996fb9a05d0a055192)


### 3.Samesite Cookie属性 

在Set-Cookie中加入属性Samesite，如 
```
Set-Cookie: foo=1; Samesite=Strict 
```
表明在任何情况下第三方网站发送请求时都不携带该Cookie 

但目前兼容性较差，且不支持子域 

 