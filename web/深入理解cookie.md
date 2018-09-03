![](https://ws1.sinaimg.cn/large/0062R7s4gy1fuwrzehwluj33v92kye8i.jpg)
## 1. 前言
朋友问我cookie是什么，用来干什么的，可是我居然无法**清楚明白简短**地向其阐述cookie，这不禁让我陷入了沉思：为什么我无法解释清楚，我对学习的方法产生了怀疑！在知乎上看到有人推荐使用费尔曼学习技巧，于是在重新学习cookie的过程中使用了该技巧来试验，效果有待验证！

在学习一个新的知识点前，我们应该明白自己的学习目标，要带着疑问去学习，该小节须要了解：
1. 什么是cookie，cookie的作用
2. cookie的工作机制，即cookie是运作流程
3. cookie的基本属性（4个）以及我们如何使用cookie

## 2. 什么是cookie

HTTP协议本身是无状态的。什么是无状态呢，即服务器无法判断用户身份。Cookie实际上是一小段的文本信息（key-value格式）。客户端向服务器发起请求，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。

打个比方，我们去银行办理储蓄业务，第一次给你办了张银行卡，里面存放了身份证、密码、手机等个人信息。当你下次再来这个银行时，银行机器能识别你的卡，从而能够直接办理业务。

## 3. cookie机制
当用户第一次访问并登陆一个网站的时候，cookie的设置以及发送会经历以下4个步骤：

**客户端发送一个请求到服务器** --》 **服务器发送一个HttpResponse响应到客户端，其中包含Set-Cookie的头部** --》 **客户端保存cookie，之后向服务器发送请求时，HttpRequest请求中会包含一个Cookie的头部** --》**服务器返回响应数据**

![](https://i.imgur.com/fXVvk2e.png)

为了探究这个过程，写了代码进行测试，如下：

我在doGet方法中，new了一个Cookie对象并将其加入到了HttpResponse对象中
```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        Cookie cookie = new Cookie("mcrwayfun",System.currentTimeMillis()+"");
        // 设置生命周期为MAX_VALUE
        cookie.setMaxAge(Integer.MAX_VALUE);
        resp.addCookie(cookie);
    }
```

浏览器输入地址进行访问，结果如图所示：

![](https://i.imgur.com/lejHvNY.png)

**可见Response Headers中包含Set-Cookie头部，而Request Headers中包含了Cookie头部。name和value正是上述设置的。**

## 4. cookie属性项

| 属性项		   | 属性项介绍 														            			 | 
| :----------- |:----------------------------------------------------------------------------------------|
| NAME=VALUE   | 键值对，可以设置要保存的 Key/Value，注意这里的 NAME 不能和其他属性项的名字一样    
| Expires      | 过期时间，在设置的某个时间点后该 Cookie 就会失效 
| Domain       | 生成该 Cookie 的域名，如 domain="www.baidu.com"   
| Path         | 该 Cookie 是在当前的哪个路径下生成的，如 path=/wp-admin/   
| Secure       | 如果设置了这个属性，那么只会在 SSH 连接时才会回传该 Cookie      


### Expires
该属性用来设置Cookie的有效期。Cookie中的maxAge用来表示该属性，单位为秒。Cookie中通过getMaxAge()和setMaxAge(int maxAge)来读写该属性。maxAge有3种值，分别为正数，负数和0。

如果maxAge属性为正数，则表示该Cookie会在maxAge秒之后自动失效。浏览器会将maxAge为正数的Cookie持久化，即写到对应的Cookie文件中（每个浏览器存储的位置不一致）。无论客户关闭了浏览器还是电脑，只要还在maxAge秒之前，登录网站时该Cookie仍然有效。下面代码中的Cookie信息将永远有效。
```java
        Cookie cookie = new Cookie("mcrwayfun",System.currentTimeMillis()+"");
        // 设置生命周期为MAX_VALUE，永久有效
        cookie.setMaxAge(Integer.MAX_VALUE);
        resp.addCookie(cookie);
```

当maxAge属性为负数，则表示该Cookie只是一个临时Cookie，不会被持久化，仅在本浏览器窗口或者本窗口打开的子窗口中有效，关闭浏览器后该Cookie立即失效。
```java
        Cookie cookie = new Cookie("mcrwayfun",System.currentTimeMillis()+"");
        // MaxAge为负数，是一个临时Cookie，不会持久化
        cookie.setMaxAge(-1);
        resp.addCookie(cookie);
```
可以看到，当MaxAge为-1时，时间已经过期

![](https://i.imgur.com/wMKUsfD.png)

当maxAge为0时，表示立即删除Cookie
```java
        Cookie[] cookies = req.getCookies();
        Cookie cookie = null;

        // get Cookie
        for (Cookie ck : cookies) {

            if ("mcrwayfun".equals(ck.getName())) {
                cookie = ck;
                break;
            }
        }

        if (null != cookie) {
            // 删除一个cookie
            cookie.setMaxAge(0);
            resp.addCookie(cookie);
        }
```

**那么maxAge设置为负值和0到底有什么区别呢？**

maxAge设置为0表示立即删除该Cookie，如果在debug的模式下，执行上述方法，可以看见cookie立即被删除了。

![](https://i.imgur.com/WwLu7kP.gif)

maxAge设置为负数，能看到Expires属性改变了，但Cookie仍然会存在一段时间直到关闭浏览器或者重新打开浏览器。

![](https://i.imgur.com/LPCnU12.gif)

### 修改或者删除Cookie
HttpServletResponse提供的Cookie操作只有一个addCookie(Cookie cookie)，所以想要修改Cookie只能使用一个同名的Cookie来覆盖原先的Cookie。如果要删除某个Cookie，则只需要新建一个同名的Cookie，并将maxAge设置为0，并覆盖原来的Cookie即可。

新建的Cookie，除了value、maxAge之外的属性，比如name、path、domain都必须与原来的一致才能达到修改或者删除的效果。否则，浏览器将视为两个不同的Cookie不予覆盖。

值得注意的是，从客户端读取Cookie时，包括maxAge在内的其他属性都是不可读的，也不会被提交。浏览器提交Cookie时只会提交name和value属性，maxAge属性只被浏览器用来判断Cookie是否过期，而不能用服务端来判断。

![](https://i.imgur.com/RPozXJ8.png)

我们无法在服务端通过cookie.getMaxAge()来判断该cookie是否过期，maxAge只是一个只读属性，值永远为-1。当cookie过期时，浏览器在与后台交互时会自动筛选过期cookie，过期了的cookie就不会被携带了。

### Cookie的域名
Cookie是不可以跨域名的，隐私安全机制禁止网站非法获取其他网站的Cookie。

正常情况下，同一个一级域名下的两个二级域名也不能交互使用Cookie，比如test1.mcrwayfun.com和test2.mcrwayfun.com，因为二者的域名不完全相同。如果想要mcrwayfun.com名下的二级域名都可以使用该Cookie，需要设置Cookie的domain参数为**.mcrwayfun.com**，这样使用test1.mcrwayfun.com和test2.mcrwayfun.com就能访问同一个cookie

> 一级域名又称为顶级域名，一般由字符串+后缀组成。熟悉的一级域名有baidu.com，qq.com。com，cn，net等均是常见的后缀。
> 二级域名是在一级域名下衍生的，比如有个一级域名为mcrfun.com，则blog.mcrfun.com和www.mcrfun.com均是其衍生出来的二级域名。

### Cookie的路径
path属性决定允许访问Cookie的路径。比如，设置为"/"表示允许所有路径都可以使用Cookie
