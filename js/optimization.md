## 缓存
1. 基于浏览器头信息的缓存
主要有两种：一种是判断静态资源http头部的Etag和Last-Modified是否修改；还有一种是根据expires过期的时间来判断的，原理一样，两种方法通常结合使用，如果没有修改就会
返回304 not modified
2. 使用app cache
html5提供了App cache来解决静态文件存储的问题，它通过将要缓存的静态文件申明在一个manifest文件清单中，然后要缓存的html里通过manifest
属性关联清单文件即可在下次载入html时优先加载缓存清单中列出的静态文件
3. localStroge存储
localStorage的存储相对cookie而言的确丰富了许多，也扩大了容量。特点：
适合data数据存储，不适合文件存储【localStorage是对象，js操作文件的效率远远低于浏览器直接操作】
4. indexedDB缓存
将数据存储在浏览器的indexedDB中，当网络断开的时候，可以从浏览器读取数据，用来做一些离线应用，而且不需要写特定的sql语句来对数据库进行操作
，他是nosql的数据形式json，这种方式容量足够，存取自由，但是不适合静态文件的存储
[文件参考]{http://jixianqianduan.com/frontend-javascript/2014/06/05/webworker-serviceworker.html}

未来的web应用是可以离线的，所以w3c新推出了service worker
5. service worker
它可以是浏览器提供给用户的一个网上代理，通过fetch拦截用户的所有请求，可以不向服务器发送，并可以将请求转向本地缓存或其他资源文件的加载，无论是数据还是静态文件，然后可以通过javascript的操作
进行增量操作数据，而且同时不阻塞浏览器的渲染过程
webworker现在可以在浏览器做多线程操作，但是比较限制；service worker是在web worker的基础上实现的，可以认为是使用了webworker的技术来处理网络请求、响应等方面的食物

## 前端如何解决跨域问题
- 背景：跨域是由浏览器的同源策略引起的，是指页面请求的接口地址，必须与页面url地址处于同域上（即域名，端口，协议相同）
这是为了防止某域名下的接口被其他域名下的网页非法调用，是浏览器对javascript施加的安全限制
- 目前常用的跨域的解决方法有：
1. jsonp：利用script标签可跨域的特点，在跨域脚本中可以直接回调当前脚本的函数，只能用于get请求
```
//客户端
<script type="text/javascript">
    var localHandler = function(data){
        alert("跨域")
    }
</script>
<script type="text/javascript" src="http://remoteserver.com/remote.js">
</script>

//remote.js代码
localHandler({
    "result": "我是参数传输数据"
})
```

2. CROS: 服务器设置HTTP响应头中Access-Control-Allow-Origin值，请求端设置origin值，如果前者包含后者，表示可以访问（acess-control-allow-origin: *;表示任何请求都接受）
解决跨域限制，目标服务器需要知道哪些地址的请求是可以响应的（说白了就是一种约定）
[参考连接：]{https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS}

下面介绍一种前端独立就能解决的跨域方案【代理和反向代理】
- 代理
代理，称为正向代理，是位于客户端与目标服务器端之间的服务器，为了从目标服务器取得内容，客户端向代理发送一个请求并指定目标（目标服务器），然后代理向目标服务器转交请求并获得内容返回给客户端，应用
如翻墙，利用香港的代理服务器
- 反向代理
浏览器正常发送请求，只是浏览器不知道返回的内容是服务器从其他的服务器获取的

## 优化目标
分析一个网页的优化目标
- 关键资源数
- 关键资源体积
- 关键资源网络来回数

![](../image/2.png)

## css异步加载
link标签的media属性
```
<link rel="stylesheet" href="xx.css" media="none">
```
如上，当一个媒体查询的结果值计算出来是false的时候，浏览器仍然会下载样式表，但是不会在渲染页面之前等待样式表的资源可用。
样式表一下载好，media属性就必须被设置为一个可用的值，以便样式规则被应用到html文档中onload事件，就可以来将media属性切换到all
```
//让页面的主要框架css执行阻塞加载（即：让重要的页面布局阻塞页面渲染），而其他使用非阻塞加载来有效提高网页呈现速度，进而提高性能
<link rel = "stylesheet" href = "css.css" media = "none" onload = "if(media != 'all') media = 'all'">
```

## webpack工具实现的前端优化
1. 提取文件的公共部分
```
entry: {
    topic: "./src/components/.../topic.js",
    activity: "./src/.../activity.js",
    react: ['react'],
    jquery: ["jquery"]
},
plugins: [
    new CommonChunkPlugin({
        name: ['jquery','react'], //将公共模块提取
        minChunks: Infinity //提取所有entry的公共依赖
    })
]
```
2. 压缩代码
```
new webpack.optimize.UglifyJsPlugin({
    compress: {
        warning: false
    }
})
```
3. 同步加载和异步加载
webpack中同步的代码会被合并并且打包在一起，异步加载的代码会被分片成一个chunk，在需要该模块的时候再加载，即按需加载；这个度要开发者自己把握，同步加载过多
代码会造成文件过大，影响加载速度；异步则文件太碎，造成过多的http请求，同样会影响加载速度
- 同步加载的写法
```
var topicItem = require("../topic/topicitem")
```
- 异步的写法
```
require.ensure([],function(){
    dialog = require("../../widget/dialog")
    $ = require("jquery")
    io = require("../../utils/io")
})
```
首屏同步加载，首屏过后异步加载
4. 缓存控制
缓存要做两件事：
- 对于没有修改的文件，从缓存中获取文件
- 对于以缓存的文件，不要从缓存中获取
解决方案：
- 不处理，等待用户浏览器缓存过期，自动更新，这是最偷懒的，命中率低一些，同时出现文件没有更新；
- http头对文件设置很大的max-age，同时给每个文件名上带有该文件的版本号，例如把文件的hash值作为版本号；当文件没有更新时，使用缓存的文件自然不会出错，
当文件已经更新时，其hash值必然改变，此时文件改变了，自然不存在此文件的缓存，于是浏览器回去加载最新的文件
webpack是可以轻松做到的，只需要给文件名配置[chunkhash:8]即可，其中8表示的是hash的长度为8，默认是16
```
output: {
    path: __dirname + "/release/",
    filename: "[chunkhash:8].[name].js",
    chunkFilename: "[name].[chunkhash:8].js"
}
```
浏览器给这种缓存方式的缓存容量太少了，只有12Mb，且部分host；所以更加极致的做法是用localStorage，以文件名为key，文件内容为value，虽然缓存容量也只有5Mb，但是每个host是独享这5Mb的
5. 自动生成页面
文件带上版本号后，每一次文件变化后，都需要手动修改引用的文件名，这种重复的工作可以使用htmlWebpackPlugin和
ExtractTextPlugin插件
- 生成带js的页面
```
new HtmlWebpackPlugin({
    filename: "topic2.html",
    template: __dirname + "src/app.html",
    inject: true,
    chunks: ['react','jquery',"topic"],
    //排序
    chunkSortMode: function(a,b){
        var index = {'topic':1,'react':3,'jquery':2},
        var aI = index[a.origin[0].name]
        var bI = index[b.origin[0].name]
        return aI&&bI?bI - aI : -1
    }
})
```
生成带css的页面
```
new ExtractTextPlugin("comm.[contenthash:9].css")
```

## h5性能最佳实践
1. 请求数优化
    - 在请求数方面，将js,css各用combel的方式合并成单个资源
    - 页面图片等等，只加载首屏资源，提升首屏展示速度
    - 使用css，svg，iconfont替换UI图片
2. 合理使用iconfont
    - 优点：自由变化大小，矢量不失真，自有修改颜色，可以添加一些视觉效果如阴影、旋转、透明度、兼容ie6
    - 使用多了会造成极大的网络宽带的消耗
3. 首屏多个动态接口合并
    - 日常业务中，一个页面往往拆分出多个异步数据接口（后端的说法是解耦），甚至首屏也需要3-5个接口（如动态banner区块，推荐内容，商品列表等），有的还有嵌套关系
4. 禁止重定向
    - 一般来说重定向会重新返回一个url，然后在发送url请求，会造成巨大的性能损耗
5. 图片优化
    - 使用webp格式，分为有损压缩和无损压缩
    - 使用sprite
6. DNS解析优化
    - 使用dns-prefetch预解析
    - 域名收敛，控制域名的数量（页面请求、静态资源（css和js）、图片资源、动态数据各一个）
7. http2.0的优化
    - 多路复用请求优化，建立一次连接可以发送多次请求
    - 服务器推送技术
    - http请求头压缩
8. 预加载和离线化方案（304缓存）
9. 渲染优化
    - 禁止使用iframe(阻塞父文档的onload事件)
    - 禁止使用gif图片实现loading（降低cpu消耗，提升渲染性能）
10. prefetch
    - 预加载，在用户需要前我们就将所需要的资源加载完毕
    - 浏览器有缓存，为何需要预加载
        - 用户可能第一次访问网站，此时还无缓存
        - 用户清空了缓存
        - 缓存过期，资源重新加载
    - chrome的预加载技术
        - 例如在搜索框输入"amaz"时，它猜测你可能要访问的网站是
        amazon.com,可能就帮你加载了这个网站的一些资源，如果预测
        准确的话，就能大大提高用户的浏览体验
    - dns prefetch
        - 将域名转化为对应的ip地址，这是一个非常耗时的过程，
        dns prefetch分析这个页面需要的资源所在的域名，浏览器空闲的时候
        提前将域名转化为ip，真正请求资源时就避免了上述的过程的时间，
        在firefox中，dns预解析是与页面并行加载的，并且不影响加载，以图片
        为主的移动网站被访问时，可以使用dns预解析
        - 应用场景一：我们的资源存在在不同的CDN中，那提前声明好这些资源
        的域名，就可以节省请求时多个域名的解析时间；应用场景二：如果我们
        知道用户接下来的操作一定会发起资源的请求，加强用户体验
    [参考连接]{http://www.jianshu.com/p/7f58ddfc1392}

## 字符串拼接的性能问题
- 由于字符串是不可变的，这意味着要创建中间字符串来存储链接结果。频繁地后台创建
和销毁字符串导致性能下降，所以建议使用数组拼接
```
var arr = []
for(var i=0;i<len;i++){
    arr.push()
}
var str = arr.join('')
```

## 懒加载（延迟加载）与预加载
1. 懒加载的原理以及步骤
    - 页面中的img元素，如果没有src属性，浏览器就不会发出请求去下载图片，只有通过javascript设置了图片路径，浏览器才会发送请求。
    在页面中把所有的图片使用用一张占位图进行占位，把正真的路径存放在元素的"data-url"(名称自己能够识别就好)属性里，要用的时候
    就取出来，再设置
    - 具体的实现步骤：首先，不要将图片地址放到src属性中，而是放到其他属性（data-original）中；页面加载完成后，根据scrollTop判断
    图片是否在用户的视野内，如果在则将data-original属性中的值取出存放在src属性中；在滚动事件中重复判断图片是否进入视野，如果进入，
    则将data-original属性中的值取出存放在src属性中
2. 预加载的原理以及方法
    - 提前加载图片，当用户需要查看时直接从本地缓存中渲染，享受极快的用户体验
    - 方法：
        1. 单纯的使用css
        ```
        #preload-01{
            backgorund: url(http://domain.tld/image-01)
        }
        //将选择器应用到html元素中，我们就可以通过css的background属性将图片预加载到屏幕外的背景处，其他页面需要调用的时候，直接在缓存中读取
        ```
        2. 单纯的js
        3. css和js混合
