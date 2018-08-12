## nodejs中的net.socket与scoket.io.js的区别
 net.socket是系统的socket接口，操作tcp，属于传输层；往上就是http了,
 http是基于tcp的；而socket.io.js是websocket协议的一种实现，属于应用层，
 html5也对websocket定义了对应的API，由浏览器等客户端程序实现它，这样最上层的
 web应用程序就可以直接使用websocket连接了