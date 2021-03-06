# 测试登出功能 (OTG-SESS-006)


### 综述

会话终止是会话生命周期的重要部分。将会话令牌生命周期缩减到最低限度降低了会话劫持攻击的成功率。这通常可以作为一个对抗跨站脚本攻击和跨站请求伪造的控制措施。这些攻击依赖用户拥有一个认证过的当前会话。如果没有安全的会话终止仅增加了这些攻击的攻击面。

一个安全的会话终止至少要求下面环节：

* 在用户界面允许用户手动登出。
* 在一定时间没有活动的情况下终止会话（会话超时）。
* 服务器端会话状态正确无效化。

存在多个问题能够阻止有效的会话终止。在理想的安全web应用程序中，用户应该能在任何时候通过用户界面终止会话。每一个页面都应该有明显的登出按钮。不清晰或有歧义的登出功能可能会引起用户不信任该功能。

另一个常见的终止会话的错误是将客户端会话令牌设置为一个新的数值，而服务器端依旧保持旧令牌有效，且可以通过复用旧的令牌的值来继续会话。有时候只向用户展示一个确认信息，而没有进一步的操作。这些问题都应该避免。

有些web应用框架只依赖于会话cookie来鉴别登录用户。用户ID是嵌入（或加密）在cookie值中。应用服务器不追踪任何服务器端的会话。当登出后，会话cookie从浏览器端移除。然而，由于应用程序服务器端不做任何记录，他无法得知会话是否已经登出。这样通过复用会话cookie，就可以重新获取认证后的会话访问权限。非常著名的例子是ASP.NET中的表单认证功能。

Web浏览器用户通常不注意应用还在线过程中就简单关闭浏览器或页面。web应用程序应该注意到这种行为，并在一定时间后从服务器端自动终止会话。

替代应用相关认证模式的单点登录（SSO）系统通常导致多个会话的并存，这些会话可能需要分别终止。举例来说，终止应用相关的会话不终止单点登录系统的会话。返回SSO入口提供了用户登回先前登出的系统的可能性。从另一方面说，SSO系统的登出也不需要导致相关连接应用的会话终止。

### 如何测试

**测试用户登出界面:**

验证在用户界面可以看见登出功能。为了这个目的，从用户视角来看，每一个页面都应该能够登出web应用。

**期待结果:**

一个良好的登出用户界面可能有如下属性：
* 所有页面存在登出按钮。
* 登出按钮应该很容易被用户识别出来。
* 在读取页面之后，登出按钮应该不需要滚动页面就能找到。
* 理想的登出按钮应该固定在浏览器可显示的页面某个区域，不随滚动条滚动改变。


**测试服务器端会话终止:**

首先，需要存储用于识别会话的cookie。调用登出功能，观察应用程序行为，特别是有关会话cookie的。尝试浏览认证会话才能访问的页面，如使用后退按钮。如果显示的是页面的缓存，点击刷新获取新页面。如果登出功能使cookie变成了一个新的值，还原旧的cookie值并尝试在认证区域进行刷新。如果这些操作都没有展示特定页面的任何漏洞，至少尝试一下更多的安全相关的页面，确保会话终止确实被应用程序正确实现了。


**期待结果:**

认证用户的任何数据都不应该出现在这个测试中。理想的，当会话终止后访问这些认证区域，应用程序应该重定向到公开区域或登录表单。虽然不是安全的应用程序必须做的，但是在登出后将会话cookie设置为一个新值是一个好的实践。


**测试会话超时:**

尝试通过增加访问认证区域的时间间隔来确定会话超时。如果发生了登出行为，那么期间的时间间隔大致就是会话超时时间的值。

**期待结果:**

由于超时引起的会话登出应该和服务器端终止的会话测试中描述的一致。

会话超时合适的时间依赖于应用程序的用途，应该在安全性和易用性直接保持一致。在银行应用中，不应该保持非活动会话超过15分钟。另一方来说，wiki系统或论坛系统中过短的超时时间会惹恼正在输入大段文章中的用户。1小时或更多的时长更加合适。


**测试单点登录环境中的会话终止（单点登出）:**

在目标应用中实施登出。确认存在中心入口或目录允许用户登回应用，而不需要重新认证。测试如果应用程序请求用户认证，应用程序的入口URL是否被请求。当登录进目标应用后，实施对SSO系统的登出。接着尝试访问目标应用的认证区域。

**期待结果:**

可以期待在调用web应用的登出功能和SSO系统自身的登出功能会引起所有会话的终止。在登出SSO系统后，应该需要用户的重新认证才能获得应用系统的访问权限。


### 测试工具

* "Burp Suite - Repeater" - http://portswigger.net/burp/repeater.html

### 参考资料

**白皮书**

* "The FormsAuthentication.SignOut method does not prevent cookie reply attacks in ASP.NET applications" - http://support.microsoft.com/default.aspx?scid=kb;en-us;900111
* "Cookie replay attacks in ASP.NET when using forms authentication" - https://www.vanstechelman.eu/content/cookie-replay-attacks-in-aspnet-when-using-forms-authentication

