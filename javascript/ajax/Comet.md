# Comet

#### Comet让信息近乎实时地被推送到页面上。

1. 短轮询 和 长轮(红宝石p589)

短轮询 是 一旦请求，服务器不管是否有效都立即返回响应。

短轮询的翻版。也就是说打开http连接后，较为长时间的保持这种状态，然后等待服务器响应


2. HTTP流

仅用一个http请求，服务器保持连接打开然后周期性的向浏览器放松数据
通过侦听XHR对象的readyState状态 ， 随着不断从服务器接收到数据 ， readyState的值会周期性变为3

每次从xhr.responseText中 拿取新的数据即可


#### 服务器发送事件

服务器响应的MIME类型必须是text/event-stream

1. SSE


2. 事件流