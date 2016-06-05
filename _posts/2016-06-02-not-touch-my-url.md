---
layout: post
title:  "魂淡别碰的孩子(接口)"
date:   2016-04-11 08:06:05
categories: 安全
excerpt: 安全
---

* content
{:toc}

![安全](http://demo.thankbabe.com/blog/images/anquan1.jpg)    

---

# 开场   

`魂淡别碰的孩子(接口)`,
作为后端程序猿自己写的`接口`就像自己的`孩子`一样，尽然制造出来了，那就要对其以后的人生负责到底；  
随着业务的壮大，需要支撑业务接口也越来越多，使用的用户也越来越多，黑客就盯上了，总是在业务中寻找着可以获得他人利益的入口，所以我们应该多考虑安全性问题，防范于未然。

---

# 场景   

服务端程序猿根据需求开发出业务相关的接口，用来满足需求中用户和服务器交互的功能，提供给前端或者客服端（PC端软件，APP端应用）使用，
大部分数程序猿在开发接口的时候就仅仅去考虑如何实现业务上的逻辑功能，而往往很少会去考虑接口的安全性问题，
一般服务端提供的接口都是http协议的，通过Fiddler，Wireshark，Charles等抓包工具，可以抓取到http请求，然后进行分析，模拟请求，进行并发请求，或者修改参数的攻击。

---

# 例子：

## 问题1. 接口暴露用户隐私信息就相当于在光天化日下裸奔，被看光了
> 描述：程序猿在做业务接口的时候往往没有保护用户隐私的意识，把用户的隐私信息暴露在外面，一旦被人利用起来会给用户带来麻烦，同时一旦被发现会降低平台的信任度；

## 防：

0. 用户隐私数据加密，加*号，如用户的相关数据的JSON中有用户手机号，用户邮箱，支付账号，邮寄地址等隐私数据；
0. 用户请求接口时需要对其隐私参数加密：如用户登陆请求登陆接口，需要将用户密码进行可逆加密，以免接口被恶意代理捕捉请求后获取明文密码；

---

## 问题2.数据被人顺手带走(主业务接口相关JSON数据 如：首页商品列表数据)
> 描述：接口中的JSON数据会被其他人拿去做自己的相关的功能；这样就造成了服务器的额外支出

## 防：

0. IP请求量限制，时间范围内请求量限制，等各种限制IP请求的规则，   
如：  
统计记录(可以记录到mongdb中)，定时监控记录发现请求量大于限制的数量就进行IP封杀;  
0. 请求头的校验，如：User-Agent 校验请求头是不是APP客服端发起，Referer 是不是有来源，来源域名是不是自己的域名地址等(这种方式只能是多一个门槛)；

---

## 问题3.恶意修改请求参数
>描述：通过修改请求中的参数来发起的请求，如：登陆接口修改用户名和用户密码，进行密码库碰撞等。

>温馨提示：  
>修改请求参数可能会导致很多安全性问题，如：SQL注入，XSS 跨站脚本攻击等，传送门我的【[大话程序猿眼里的WEB安全](http://blog.thankbabe.com/2016/04/03/Safe/)】有相关的介绍和解决方案    
>以下方案都针对客户端，如PC软件和APP，WEB端JS去做的话JS代码都是暴露出来的所以建议一定要混淆JS代码

## 防：

0. 增加一个校验签名参数，将参数进行逻辑的组合拼接MD5，这样可以使参数无法被修改，修改了就提示非法请求。
如：
接口http://www.test.com/go/?actid=1&userid=123 我们可以加一个sign参数= MD5(actid=1&userid=123&【secret】)【secret】=秘钥，自己定义。
使用者用一样的逻辑得到密文和sign签名进行对比是否一样，不一样就提示非法请求。

0. 整个参数内容进行可逆的加密

---

## 问题4.模拟请求
>描述：通过抓包工具抓到请求后模拟请求，模拟每日签到请求，或者直接发起每日签到的并发请求。     
>温馨提示：并发请求后如何保证数据的一致性问题，传送门我的【[大话程序员眼里的高并发](http://blog.thankbabe.com/2016/04/01/high-concurrency/)】有相关的介绍和解决方案。

### 防:
0. 模拟并发请求，IP限制同上问题2的解决方案。

---

# 总结
我们需要提高自己的安全意识，防范于未然，要多站在攻击者的角度来看自己的接口;(让自己有一种被害妄想症的感觉，你就离成功近了一步，<(￣︶￣)↗ )
不要做开发需求的机器人，我们是有思想有创造力的开发者;

---

### 附加个人开发流程

> 在评审需求的时候要把业务逻辑问题提出来，并给予解决方案的选择;   
> 确定需求后将整个业务逻辑的梳理清楚，复杂的可以画出流程图;  
> 根据需求设计实现方案，需要考虑性能问题[数据库压力，服务器压力]，安全问题，用文档的形式记录下自己的设计方案。（可以深入到代码层面如何去实现）
> 列出需求中功能点，评估出自己的时间，得到总工时
> 开始开发，开干

---
![哈哈](http://demo.thankbabe.com/blog/images/ms.jpg)

> 转载请申明原地址，谢谢合作   
> 如有任何想说的请留言哦，我会根据大家的建议修改有疑义的内容和新增总结    
> 欢迎大家关注我的[Github](https://github.com/SFLAQiu)   