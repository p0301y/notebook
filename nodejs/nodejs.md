## nodejs中的net.socket与scoket.io.js的区别
 net.socket是系统的socket接口，操作tcp，属于传输层；往上就是http了,
 http是基于tcp的；而socket.io.js是websocket协议的一种实现，属于应用层，
 html5也对websocket定义了对应的API，由浏览器等客户端程序实现它，这样最上层的
 web应用程序就可以直接使用websocket连接了

## ssr
我觉得ssr最大的问题不在前端框架友不友好，而是你用什么去做后端的渲染，假如php，前后端代码怎么分离，至少需要个适配层吧;用node吧，多了一层，中间的服务治理、模板缓存、数据缓存、性能要看你能不能hold住，能hold当然很好
只是单纯为了说加快首屏的呈现，我觉得某些场景预渲染+骨架屏是个比较好的偷懒方式