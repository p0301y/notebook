1. http1.1的特点
    - 持久连接：每个TCP连接开始都有三次握手，要经历一次客户端与服务器间完整的往返，而开启了持久连接就不必每次
    都要握手，在http请求头中增加了connection: keep-alive
    - HTTP管道：持久HTTP多次请求必须严格满足先进先出（FIFO）的队列顺序：发送请求，等待响应完成，再发送客户端队
    列中的下一个请求
    - 度量和控制协议开销
    - 连接与合并
    - 嵌入资源
2. https的原理
用SSL或TLS协议对HTTP协议传输的数据进行加密
验证流程：
    - 客户端发起一个https请求，把自身支持的一系列密钥算法套件（Cipher）发送给服务器
    - 服务器接受客户端的所有Cipher后与自身支持对比，如果不支持则连接断开，反之则选择
    一种加密算法和hash算法以证书的形式返回给客户端，证书中还包括了公钥、颁证机构、网址
    失效日期等
    - 客户端收到服务器的响应后会做以下几件事
        - 验证证书的合法性
        - 生成随机密码
        - Hash握手信息
    - 服务器端拿到客户端传来的密文，用自己的私钥来解密握手消息取出随机数密码，再用随机数密码
    解密握手消息与HASH，并与传过来的hash值比较确认是否一致；然后随机密码加密一段握手消息（握手
    消息+握手消息的hash值）给客户端
    - 客户端用随机数解密并计算握手消息的hash，如果与服务器的hash值一致，握手结束，之后所有的通信
    数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密；因为这串密钥只有客户端和服务器端
    知道，所以即使被拦截也是无法解密数据的，一次保证了通信安全
    非对称加密算法，对称加密算法，hash算法

3. SPDY协议
    - SPDY与HTTP相比较的优点：支持多路复用，实现请求优化；支持服务器推送技术；压缩了Http报文的首部信息，
    节省传输数据宽带；强制使用SSL传输协议，全部由SSL加密，信息传输更加安全

4. fetch和XMLHttpRequest
    fetch是XMLHttpRequest的一种替代方案
    jquery1.5以后映入$.deffered功能，使得ajax的写法类似于fetch，fetch的支持情况不是很好
    ```
    var myHeeaders = new Header()

    var myInit = {
        method: "GET",
        headers: myHeaders,
        mode: "cors",
        cache: 'default'
    }
    fetch("flowers.jpg",myInit).then(function(response){
        return response.blob()
    }).then(function(myBlob){
        var objectURL = URL.createObjectURL(myBlob)
        myImage.src = objectURL
    })
    ```
    - fetch的优点：
    1. 语法简单，更加语义化
    2. 基于标准的Promise实现，支持async/await
    3. 同构方便，使用isomorphic-fetch
    - 如果兼容ie8的话，需要引入ployfill