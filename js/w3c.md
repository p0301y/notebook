## 可维护性
- 可理解性：后期的维护人员容理解代码并且进行维护
- 直观性：不管操作或者说是业务有多么的复杂，代码中的东西一看就能够明白
- 可适应性：代码以一种数据上的变化，不要求完全重写方法
- 可扩展性：在代码架构上已经考虑到未来允许对核心功能进行扩展
- 可调试性：当有地方出错的时候，代码可以给予足够的信息直接确定问题的所在
## 代码约定
1. 可读性：必要的注释，良好的有意义的命名，代码的层次分明，代码简化，去除多余的代码
2. 松散耦合：解耦html/js，解耦css/js，解耦应用逻辑/事件处理程序，封装与重用
3. 安全：尊重对象所有权（对于js原生的固有对象，不要随便在原型上添加方法；对于自定义
局部使用对象可以使用，特别是在面向对象编程的时候）；避免全局变量（使用一个字面量对象存储
变量，或者使用立即执行函数）；对于不变的值使用常量const；避免与null进行比较，，可以使用
instanceof\typeof替换
4. 性能：避免内存溢出；避免全局查找（如果局部多次使用全局变量如document,可以使用一个局部变量
保存document）；避免使用with，with会创建自己的作用域，会增加其中执行代码的作用域链的长度
；循环的时候一定要确保终止循环的条件；原生方法比较快，switch比if-else快，位运算比较快；最小化
语句（比如声明多个变量，用一个var即可）；优化dom交互（如插入dom，避免多次插入，每次插入都会
造成reflow，使用documentFragment一次性插入；页面上创建dom节点的方法有createElement()\appendChild()
\innerHTML，对于大的dom更改，使用innerHTML要快得多【当innerHTML设置为某个值的时候，后台会创建
一个html解析器，然后使用内部dom调用来创建dom，而非基于javascript的dom调用，内部方法是编译好的
而非解释执行的，所以快的多】；使用事件代理（事件委托）；注意使用HTMLCollection）；js压缩和http压缩

## js中的new
- new一个函数隐藏的操作
 ```javascript
 function Animal(name){
   this.name = name
 }
 new Animal("cat") ==>{
    var obj = {}
    obj.__proto__ = Animal.prototype
    var result = Animal.call(obj,"cat")
    return result === 'object' ? result : obj
 }
 ```
 1. 创建一个空对象obj
 2. 把obj的__proto__指向了构造函数Animal的原型对象prototype
 此时便建立了原型链：obj - Animal.prototype - Object.prototype - null
 3. obj对象调用Animal的环境（很重要）
 4. 确定返回的对象
 - new的意义
 通过new创建的对象和构造函数之间建立了一条原型链，原型链的建立
 使得原本孤立的对象有了依赖和继承能力，体现了面向对象的本质

 ## 前端模块化
 1. 为什么需要模块化？

    代码量的骤增 => 分制管理的刚性需求
 2. 模块化方案需要解决的问题？

    要实现的问题：模块封装和模块加载
    - 如何定义模块以确保模块的作用域独立，避免命令冲突？
    - 如何管理模块之间的依赖关系，避免重复加载与循环引用？
    - 模块化代码如何部署，以降低http请求数？
    - 如何实现按需加载？
 3. 原始的解决方案以及局限性？
    命名空间+立即执行函数+script标签

    局限性：
    需要手动管理依赖，不具备可扩展性；无法实现按需加载
 4. 新的解决方案

     1.commonjs：module.exports暴露接口，require()获取资源；使用在服务器端的同步加载模块
     并不适合浏览器（网络延时、异步特性）
     ```javascript
     var math = require("math")
     math.add(2,3)
     ```
     如上述同步代码，第二行必须等待第一行require之后运行，换句话说必须等待math.js
     加载完成；浏览器的加载取决与网络，如果响应时间很长，真个应用都会停在那里；
     而服务器端不存在，所有模块都在本地硬盘，可以同步加载，等待时间短；因此，在浏览器
     端出现了各类模块加载器，以解决模块加载问题，各类模块加载器提出了各自的模块封装的规范
     ，其中sea.js提出/实现的封装规范，称为CMD规范；requirejs提出/实现的封装规范，就是AMD
     规范
     2.seajs：模块封装
     ```javascript
     define(function(require,exports,module){
         var a = require("./a") //模块加载
         a.dosomething()
         //...
         var b = require("./b") //依赖可以就近书写
         b.dosomething()
         //通过exports对外提供接口
         exports.doSomething
         //或者是通过module.exports提供整个接口
         module.exports = ...
     })
     ```
     模块加载：
     ```javascript
     var $ = require("jquery")
     ```
     3.requirejs
     模块封装
     ```javascript
     define(["./a","./b"],function(a,b){
        a.dosomething()
        //...
        b.dosomething()
        return function(){} //返回模块的值，可以是函数也可以是对象
     })
     ```
     模块加载
     ```javascript
     require(["./a","./b"],function(){
        //do something
     })
     ```
     区别：
     requirejs: 依赖前置，提前加载
     seajs: 依赖就近，延时加载

     补充：
     - 什么是循环依赖：在a执行到require("b")的地方停下来去调用b，当去执行b的时候
     执行到一半require("a")，就停下来去调用a，就这么形成了循环依赖，死循环
     - require.js如何处理的：require.js只会执行到相互引用的部分，下面的代码不再过问
     ，也就是写再多也调不到
     - 循环导致的问题：开发时，在a中调用b的某个方法会报错，"xxx is not function" ,原因是b还没执行到该
     方法，得到的是null值
     - 解决办法：当出现循环依赖的时候，就不要依赖前置加载了，在a需要调用b的某个地方就近加载
     var b = require("b"),然后再去调用b的方法
     - 其他：循环依赖不会出现在ES6的import中，因为ES6中执行到import的时候并不会去执行import代码
     ，只是返回一个引用，等到真正用到的时候才会去执行
5. 大佬对前端工程化的一些理解
- 前端开发应该是“自成体系”的，包括运维部署、日志监控等
- 针对不同的场景有不同的解决方案，并不是一套大而全的框架体系。比如针对以产品宣传展示为主的网页（site）,采用多页模式和响应式设计开发；
以用户交互为主的且无强烈SEO要求的应用（Application）,采用单页面模式开发
- 产品组件化，为提高复用性尽量将组件的颗粒度分细一些，且低耦合高内聚
- 避免重复造轮子，引入一些优秀的开源资源，取长补短
6. 前端工程化体系的三大模块
- Node服务层：主要做数据的代理和Mock，url的路由分发，还有模版渲染
- web应用开发层：主要专注web交互体验
- 前端运维层：构建部署、日志监控
## 说说移动端与pc端的区别
- 移动端的网络考虑，3G4G而且没有pc端稳定，这就考虑到加载问题，懒加载和按需加载
- 移动端的分辨率各不先同，所以考虑到的是响应式布局
- 兼容性问题，移动端的浏览器的内核几乎都是webkit，所以css3和html5都可以使用，不像pc端考虑的兼容性很多，各种内核
- 交互模式pc端更加复杂，移动端基本是触摸、滑动和感应

## HTML的Doctype作用？严格模式与混杂模式如何区分？它们有何意义？
1. <!Doctype>声明位于文档的最前面，处于<html>标签之前，告知浏览器的
解析器以什么文档类型规范来解析规范
2. 严格模式的排版和js的运作模式是以该浏览器的最高标准运行。在混杂模式中，页面以宽松的
的向后兼容的方式显示。防止老的站点无法工作
3. DOCTYPE不存在或格式不正确会导致文档以混杂模式呈现

## http2.0带来的前端优化
1. 请求头压缩：http每次请求会携带大量的冗余信息，浪费了很多宽带资源，压缩之后节省流量
2. 多路复用技术（单个tcp连接以异步的方式收发http请求和响应,stream流的方式），直白的说就是所有的请求都是通过一个tcp连接完成并发的，提高了下载的效率
3. http2.0采用二进制格式传输数据，而非http1.0的文本格式，二进制在协议的解析和优化扩展上
带来了更多的优势和性能
4. server push:服务器端能够更快的把资源推送给客户端
补充：多路复用与http1.0的keep-alive(持久性连接的区别)[goto]{https://www.nihaoshijie.com.cn/index.php/archives/698/}
## html5带来的前端优化
1. service worker:处理网络请求的后台服务。适用于离线和后台同步数据或者推送信息。不能直接和dom
交互，通过postMessage方法交互（出于安全考虑，目前只能在https的环境中使用service worker）
2. web worker:模拟多线程，允许复杂计算功能的脚本在后台运行而不会阻碍其他的脚本的运行，适用于处理器
占用量大而又不阻碍的情形，不能直接与dom交互，通过postMessage交互

## 前端静态资源的缓存和更新问题解析
为什么是静态资源需要缓存？
顾名思义，静态资源是静态的，一般不会改动
浏览器缓存主要有两类
缓存协商：last-midified,Etag
彻底缓存：cache-control,Expires
200状态：当浏览器本地没有缓存或者下一层失效时，或者用户点击了CTL+F5时，浏览器直接去服务器下载新的数据
304状态：这一层是由last-modified/etag控制，当下一层失效时或者用户点击fresh，F5时，浏览器就会发送请求给服务器
，如果服务器端没有变化，则返回304给浏览器
200状态（from cache）：这一层由expires/cache-control控制；expires（http1.0版有效）是绝对时间；cache-control（http1.1版有效）
相对时间，两者都存在的时候，cache-control覆盖expires，只要是没有失效，浏览器只访问自己的缓存
1. last-modify
在浏览器第一次请求某一个url的时候，服务器端返回的状态码会是200，内容是你请求的资源，同时有一个last-modified的属性标记（HttpResponse Header)
此文件在服务器端最后被修改的时间，格式类似这样：
Last-Modify：Tue,24 Feb 2009 08:01:04 GTM
客户端在第二次请求此URL的时候，根据http协议规定，浏览器会像服务器传送If-Modify
-Since报头（HttpRequestHeader),询问该时间之后文件是否有被修改过：
If-Modify-Since:Tue,24 Feb 2009 08:01:04 GMT
如果服务器端的资源没有变化，则自动返回HTTP304(NotChanged)状态码，内容为空，这样就节省了传输数据量。
当服务器代码发生改变或者重启服务时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重新
发出资源，也保证服务器有变化时，客户端能够得到最新的资源
注：如果If-Modified-Since的时间比服务器当前时间（当前的请求时间request_time）还晚，会认为是个非法请求
2. Etag工作原理
http协议规格说明定义ETag为“被请求变量的实体标记”。简单点即服务器响应时给请求URL标记，并在htpp请求头中
将其传送到客户端，类似服务器端返回的格式：
Etag:"ajfldajkfldajlf:3239"
客户端的查询更新格式是这样的：
If-None-Match: "jfjaklfjklajf:3239"
如果ETag没有改变，则返回状态304
注意：Etag在使用时候需要注意相同资源多台web服务器的Etag的一致性
3. expires
expires是用来控制请求文件的有效时间，一般是由服务器端设置的，如果带有expires
则在expire过期前不会发生htpp请求，直接从缓存中读取，用户强制F5例外
4. 区别
last-modify和expires针对浏览器，而Etag与客户端无关，所以可以适合REST架构
两者都应用在浏览器端的区别是：expires如期到达之前，浏览器不会发出新的请求
，除非用户按浏览器的刷新，所以last-Modified和expires基本是降低浏览器向服务器
发送的请求次数，而Etag更侧重于客户端与服务器之间的联系
5. last-modify,Etag,Expire混合
通常last-Modify,Etag,Expire是一起混合使用的，特别是last-Modify和Expire经常一起使用
因为Expire可以让浏览器不发起http请求，而浏览器强制F5的时候又有Last-Modify,这样就很好
的达到了浏览器端缓存的效果
Etag和Expire一起使用的时候，先判断Expire，如果已经过期，再发起http请求，如果Etag也过期了
，则返回200响应，如果没有过期则返回304响应
三者同时使用，先判断Expire，然后发送http请求，服务器先判断last-modified,再判断Etag，
必须都没有过期，才能返回304响应

## http请求头常用的字段
Accept: text/plain 设置接受的内容类型
Accept-Charset: utf-8  设置接受的字符编码
Accept-Encoding: gzip,deflate  设置接受的编码格式
Accept-time/Accept-language...
Authorization: Basic QWxhZGRJKLSJDFKLJSA==  设置http省份证的凭证
Cache-Control: no-cache  设置缓存机制必须遵循的指令
Connection: keep-alive
Content-Length:348
Date:....
Forwarded:for=192.0.2.03;proto=http;by=203.0.113.43 披露客户端通过http代理连接web服务器的源信息
Host：...
Apgrade: HTTP/2.0,HTTPS/1.3,websocket 请求服务器端升级协议
If-Match和If-Modify-Since  与缓存相关

## js中的浅拷贝和深拷贝
1. 对于字符串类型，浅复制就是对值的复制，对于对象来说，浅复制是对对象地址的复制，并没有
开辟新的栈，也就是复制的结果是两个对象指向同一个地址，修改其中一个对象的属性，则另一个对象
的属性也会改变，而深复制则是开辟新的栈，两个对象对应的地址不同，修改一个对象的属性，不会改变
另一个对象的属性
浅拷贝函数：
```javascript
function copy(obj){
    var childs = {}
    for(var key in obj){
        childs[key] = obj[key]
    }
    return childs
}
```
深拷贝函数：
```javascript
function deepCopy(obj){
    var o
    switch(typeof obj){
        case 'undefined': break;
        case 'string'   : o = obj + "";break;
        case 'number'   : o = obj - 0;break;
        case 'boolean'  : o = obj;break;
        case 'object'   :
            if(obj === null){
                o = null
            }else{
                if(obj instanceof Array){
                    o = []
                    for(var i=0;len=obj.length;i++){
                        o.push(deepCopy(obj[i]))
                    }
                }else{
                    o = {}
                    for(var k in obj){
                        o[k] = deepCopy(obj[k])
                    }
                }
            }
            break;
            default:
                o = obj;break;
    }
    return o
}
//深拷贝函数二
function deepCopy(obj){
    if(!obj) return;
    var newObj = obj instanceof Array ? [] : {};
    for(var prop in obj){
        newObj[prop] = typeof obj[prop] === "object" ? deepCopy(obj[prop]) : obj[prop];
    }

    return newObj
}
//巧妙的拷贝
function deepCopy(obj){
    return JSON.parse(JSON.stringify(obj))
}
```
## float与position:absolute的区别
float脱离文档流的时候，其他盒子会无视他，即和浮动盒子重叠，但是它的内容即文本会让出地方
这也是float设计的初衷，让文本环绕图片；而position：absolute脱离文档流后，其他的盒子和文本
都会无视
## 面向对象及其特性
面向对象仅仅是一个概念或者编程思想，不依赖某一个语言；就js而言，一切事务都是对象
；对象就有封装和继承的特性；java是基于类的面向对象，js是基于原型的面向对象
## BFC
1. 定义
块级格式上下文，也可以说是一个上下文对象，从上倒下，从左到右的基于盒子模型的流式布局
2. 规则
- 内部的box会在垂直方向，一个接一个的放置
- 每个元素的margin box的左边，与包含块border box的左边向接触（从左向右的格式），即使浮动也是如此
- box垂直方向的距离由margin决定，属于同一个BFC的两个相邻box的margin会发生重叠
- BFC的区域不会与float box的内容区域重叠
- BFC页面是一个独立的容器，里面的盒子不会影响到外面的元素
- 计算BFC的高度时，浮动元素也参与计算
3. 哪些元素会生成BFC?
根元素、float属性不为none、position为absolute和fixed、display为（inline-block，table-cell，table-caption，flex，inline-flex）
overflow不为visible的时候
[参考链接]{http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html}

## 单例模式
在应用单例模式时，生成单例的类必须保证只有一个实例的存在，很多时候整个系统只需拥有一个全局对象，才有利于协调系统
的整体行为，比如整个系统的配置文件中，配置数据有一个单例对象进行统一的读取和修改，其他对象需要配置数据的时候也统一通过该
单例对象来获取配置数据，这样就可以简化复杂环境下的配置管理
单例模式的思路：一个类能够返回一个对象的引用（并且永远是同一个）和一个获得该实例的方法（静态方法，通常交getInstance名称），
那么当我们调用这个方法的时候，如果类持有的引用不为空就返回该引用，否则就创建该类的实例，并且将实例引用赋值给该类保持的那个引用再返回
同时将该类的构造函数定义为私有方法，避免其他函数来使用构造函数实例化对象，只通过该类的静态方法来得到唯一的实例
```javascript
//最好的实现方法：使用闭包来私有化数据，通过single.getInstance()来获取单例
var single = (function(){
    var unique;
    function getInstance(){
        if(unique === undefined){
            unique = new Construct()
        }
        return unique
    }

    function Construct(){
        //生成单例的构造函数的代码
    }

    return {
        getInstance:getInstance
    }
})()
//实现方法一：最简单的对象字面量,缺点是没有封装
var single = {
    attr: 1,
    method: function(){
        return this.attr
    }
}
var t1 = singleton
var t2 = singleton
t1 === t2  //true
//实现方法二：构造函数的内部判断
function Construct(){
    //确保只有单例
    if(Construct.unique !== undefined){
        return Construct.unique
    }
    this.name = "name"
    this.age = 24
    Construct.unique = this
}
var t1 = new Construct()
var t2 = new Construct()
t1 === t2 //true
```

## 常见的http状态码
有关缓存：第一次访问200，第二次访问(cache),按F5刷新304，按ctrl+F5强制刷新200
301，302的区别：(重定向需要结合http响应头中的location字段)
301重定向是永久的重定向，搜索引擎在抓去新的内容的时候也将旧的网址替换为重定向之后的网址；
302重定向是临时的重定向，搜索引擎会抓去新的内容而保留旧的网址，因为服务器返回的是302，所以搜索引擎认为这只是暂时的，所以会保留旧的网址
400：错误请求，服务器不理解请求的语法
401：未授权，请求身份验证，对于登录后的请求的网页，服务器可能返回此响应；
403：禁止，服务器拒绝请求
404：未找到，服务器找不到请求的资源
500：服务器遇到错误，无法完成请求
502：错误网关，服务器作为网管或代理，从上游服务器收到无效响应
503：服务不可用，服务器目前无法使用（由于超载或停机维护）,通常这只是暂存状态

## css兼容性
1. 了解浏览器的兼容情况(css查询)：
- caniuse.com
- MDN CSS Reference
- Codrops CSS Reference
- QuirksMode.org CSS

2. 方案
- 如果低版本浏览器没有这个特性可以接受吗？
    - border-radius不支持时，没有圆角
    - box-shadow不支持时，没有阴影
- 可以使用效果稍微差一些的替代方案吗？
    - min-height: 100vh,用min-height: 800px
- 可以使用一些替代方案吗？
    - opacity: 0.5在IE下使用filter: alpha(opacity=50)
- 可以使用javascript让浏览器支持吗？
    - 使用html5shim.js让IE6 - 8支持html5新标签
    - 使用DD_belatedPNG.js让IE6支持半透明png图片
- 更换实现方式
    - 布局
    - 例如圆角使用图片代替

3. 不同浏览器使用不同的样式
- @supports：查询是否支持，类似与media
- 层叠
- 条件注释
- 浏览器怪癖
```
//@supports
.container{
    display: flex;
}
@supports(display: grid){
    .container{
        display: grid;
        grid-template: repeat(4, 1rf) / 59px 100px;
    }
}
//层叠样式
div{
    display: table;
    display: flex;
}
//条件注释
<!-- [if IE 7 ]>
    <p>只有Ie7能够看见我</p>
<![endif]>
//浏览器怪癖
ie6不支持两个类选择器直接组合
.unused-class.selector{

}
.container{
    height: 100px;
    /*只有ie6会忽略_，所以ie下会显示200px*/
    _height: 200px;
}
.container{
    height: 100px;
    /* ie7支持* */
    *height: 200px;
}
iie6 - 8不支持:root选择器
:root .selector{
    /* ie6-8 style */
}
.selector{
    color: #fff;
    /* ie6 - 8会忽略\9 */
    color: #fff\9;
}

//同时适配ie
.tip{
    background: blue;
    background: red\9;
    *background: black;
    _background: orange;
}
/*
    ie8不支持
    background-size(ie8下使用固定宽度布局)
    border-radius
    box-shadow
    opacity(filter:alpha(opacity=50))
    rgba\hls\hlsa(一般情况下使用相近的不透明色代替)
    rem/vh/vw/calc(降级为固定宽度)
    media query(引入respond.js)
*/
/*
    ie9不支持
    transition与animation（可以使用javascript）
*/
/*
    控制ie模式
    meta标签控制进入哪种文档模式
    <meta http-equiv="X-UA-Compatible" content="IE=7">//使用ie7渲染
    <meta http-eqiuv="v-ua-compatible" content="IE=edge">//使用最新引擎
*/
```

## 行内元素和块级元素
1. 行内元素的特点
 - 和其他行内元素都在一行上
 - 设置宽高无效，根据内容的大小改变而改变
 - 设置margin无效，只有左右margin有效，上下无效
 - 设置padding无效，只有左右padding有效，上下无效
 - 行内元素只能容纳文本或者其他的行内元素
2. 块级元素的特点
 - 总是从新行开始
 - 高宽、行高以及外边距和内边距都是可以控制的
 - 宽度缺省是它容器的100%，除非设定一个宽度
 - 它可以容纳行内元素和其他的块级元素
[常见的块级元素与行内元素]{http://www.cnblogs.com/WebShare-hilda/p/4708381.html}

## 自定义事件的两种方法
1. 使用new Event()【推荐】
```
var btn = document.querySelector('.button')
var ev = new Event("test",{
    bubbles: 'true',
    cancelable: 'true'
})
btn.addEventListenr('test',function(event){
    console.log("hello,it is me")
},false)
btn.dispatchEvent(ev)
```
2. document.createEvent()
```
//只是将创建事件和初始化事件分成了两个步骤
var preview = document.createEvent("Event")
preview.initEvent("preview",true,true)
preview.addEventListener("preview",function(){
    console.log("it is me")
},false)
btn.dispatchEvent(preview)
```