# HTTP连接的处理

#### Connection首部字段介绍
> \<<HTTP权威指南里>>p91页 标题写到常被误解的Connection首部

背景：HTTP允许在客户端和最终的目的端服务器之间存在一系列HTTP中间实体(代理),
HTTP报文会逐步得经过这些中间设备，转发到目的端服务器上。那问题来了，可能有些情况下，你不想把某些字段
转发到另一个代理上怎么办。Connection提供了这一个功能。

> HTTP的Connection首部字段中有一个逗号分割的连接标签列表，这些标签为此连接指定了一些不会传播到其他
> 连接中去的选项。

三种不同类型的标签：
* HTTP首部字段名，列出只与此连接有关的首部(例如: Keep-Alive字段)
* 任意标签值，用于描述此链接的**非标准选项**(意思就是说非标准里的首部字段)
* 值close，意味着操作完成后需永久关闭这条持久连接

例如这样 
```
HTTP/1.1 200 OK
Cache-Control: max-age=3600
Connection: meter,close,bill-my-credit-card
Meter: max-uses=3,max-refuses=6,dont-report
```
对照**三种不同类型的标签**，
meter只能在这个连接中使用，所以在转发前会把这个字段给删除
close代表HTTP操作事物完成后关闭该连接
bill-my-credit-card: 这个是**非标准选项**，要应用假象的bill-my-credit-card选项

这里HTTP权威里说明的不怎么好理解，贴个[问题连接](https://segmentfault.com/q/1010000012976579)

#### 串行事务处理时延

假设每个HTTP请求都需要建立一个TCP连接，无疑这回带来很多的问题。

1. 时间延长的问题
2. 比方说你有请求一张图片，但如何放置图片得了解图片的具体尺寸信息才能处理。那么假设在多个http的请求下，图片请求却搁置在后面，就会影响用户的视觉体验。



#### 解决方法

1. 并行连接

HTTP允许客户端打开多条连接，并行执行多个HTTP事务。每个事务都有自己的TCP连接

缺点：
* 带宽速度慢，导致几个连接效率变慢，还是慢（但是在现在这个环境下，应该这个问题出现频率会很小）
* 如果 连接数太多里 内存资源占用率高，所以浏览器规定是4个

2. 持久连接

即我们经常能看到 Connection: keep-alive

因为我们通常访问一个网站 ， 然后会去同一个站点请求资源。(站点本地性的特点)
HTTP/1.1(以及HTTP/1.0的各种增强版本) 允许HTTP设备在事务处理结束之后将TCP连接保持在打开状态，
以便未来的请求重用这个连接。

优点：
* 避免缓慢的连接建立机制
* 避免慢启动的拥塞适应阶段（🤔这个不怎么理解）

**❗️注意**
这个 keep-alive 只是请求连接保持在活跃状态。但是客户端和服务器可不一定会同意，且可以随时关闭连接，
还能限制连接处理HTTP事务的数量。

还有就是有个首部字段是: Keep-Alive
* 响应首部参数max 例如 Keep-Alive: max=5 代表服务器还希望处理多少个事务在这个连接上，非承诺
* 响应首部参数timeout 例如 Keep-Alive: timeout=120 代表服务器希望连接保持在活跃的时常单位秒，非承诺
* 任意未经处理的属性 name =[value] 用于测试和诊断
这个字段用来调节连接的行为

其他限制和规则，HTTP权威指南p99

**keep-alive和哑代理**

1. 盲中继问题(代理不认识connection这个字段)
在Connection首部字段中讲过，Connection这个首部字段提供了不向下一个代理转发消息的功能。

假如这样的情况:
客户端 <=> 代理 <=> 服务器

客户端请求中携带connection 发送到代理，代理**不理解** 又发给了服务器。这就导致了客户端和服务器是认为
代理是要保留连接状态的，而代理会等待服务器的关闭。而客户端再在这条连接上发送请求时，代理会忽略。直到客户端
和服务器因超时关闭连接时。因为这个原因，现代代理connection里的标准字段都不会被转发。

2. 插入proxy-connection

这个方法十分巧妙 简单来说就是
盲中继不懂proxy-connection字段，聪明的代理则会把这个字段改成connection转发给服务器。
结果就是盲中继和客户端、服务器不会建立持久连接
但是聪明的代理会建立连接。
![Proxy-Connection首部修正了单个盲中继的问题](https://github.com/workINgithub/web_study/blob/master/protocols/HTTP/images/Proxy-Connection.png)

### HTTP/1.1

> HTTP/1.1 逐渐停止了对keep-alive连接的支持，用一种名为持久连接(presistent connection)的改进型设计取代了它。

默认激活持久连接，除非声明Connection:close

#### 持久连接的限制和规则 书p104

#### 管道化连接

优势：当第一条请求通过网络流向另一端的服务器时，第二条和第三条也可以发送了。