# XSS

XSS = cross site script 区别于CSS(Cascading Style Sheet) 所以叫XSS

> XSS 攻击是指 **攻击者在网站上注入恶意的客户端代码** (这一点从 英文全名 script也能看出)，通过恶意脚本对客户端网页进行篡改，从而在用户浏览网页时，对用户浏览器进行控制或者获取用户隐私数据的一种攻击方式。

> 攻击者对客户端网页注入的恶意脚本一般包括 JavaScript，有时也会包含 HTML 和 Flash。有很多种方式进行 XSS 攻击，但它们的共同点为：将一些隐私数据像 cookie、session 发送给攻击者，将受害者重定向到一个由攻击者控制的网站，在受害者的机器上进行一些恶意操作。

XSS攻击可以分为3类：反射型（非持久型）、存储型（持久型）、基于DOM。

#### 反射型
> 反射型 XSS 只是简单地把用户输入的数据 “反射” 给浏览器，这种攻击方式往往需要攻击者诱使用户点击一个恶意链接，或者提交一个表单，或者进入一个恶意网站时，注入脚本**进入攻击者的网站。**


可以启动 app.js和app2.js文件 然后打开服务器访问http://localhost:8012并点击xss按钮
通过(伪造的)XSS注入 你可以看到然后 你可以在app2.js的终端看见 服务器成功获取了app.js

这里复习下cookie在CORS中的使用
1. 请求中得开启携带 xhr.withCredentials
2. 响应中得携带请求头 "Access-Control-Allow-Credentials" : "true"

#### 存储型
存储型 XSS 会把用户输入的数据 "存储" 在服务器端，当浏览器请求数据时，脚本从服务器上传回并执行。这种 XSS 攻击具有很强的稳定性。
比较常见的一个场景是攻击者在社区或论坛上写下一篇包含恶意 JavaScript 代码的文章或评论，文章或评论发表后，所有访问该文章或评论的用户，都会在他们的浏览器中执行这段恶意的 JavaScript 代码。


#### 基于DOM
基于 DOM 的 XSS 攻击是指通过恶意脚本修改页面的 DOM 结构，是纯粹发生在客户端的攻击。


#### 解决方案

**HttpOnly 防止劫取 Cookie**
这个字段其实就是指 该字段**只能通过HTTP请求去修改，不能通过JS操作**。但是在跨域请求访问时，仍然会携带
cookie信息

**输入检查**
>  对于用户的任何输入要进行检查、过滤和转义。建立可信任的字符和 HTML 标签白名单，对于不在白名单之列的字符或者标签进行过滤或编码。

```
// vuejs 中的 decodingMap
// 在 vuejs 中，如果输入带 script 标签的内容，会直接过滤掉
const decodingMap = {
  '&lt;': '<',
  '&gt;': '>',
  '&quot;': '"',
  '&amp;': '&',
  '&#10;': '\n'
}
```

**输出检查**

用户的输入会存在问题，服务端的输出也会存在问题。一般来说，除富文本的输出外，在变量输出到 HTML 页面时，可以使用编码或转义的方式来防御 XSS 攻击。例如利用 sanitize-html 对输出内容进行有规则的过滤之后再输出到页面中。