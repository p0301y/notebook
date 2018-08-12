## juery知识分类
1. $.ready(): 当dom已经加载，并且页面（包括图像）已经完全呈现时，会发生ready事件
```
$(document).ready(function(){
    ...js
})
```
2. $(" "): 选择器（重点无new构造）
```
(function(window,undefined){
    var rootjQuery = window.document
    var jQuery = (function(){
        var jQuery = function(selector,context){
            return new jQuery.fn.init(selector,context,rootjQuery)
        }

        jQuery.fn = jQuery.prototype = {
            constructor: jQuery,
            init: function(selector,context,rootjQuery){
                var that = this
                that.ele = null
                that.value = ''
                if(selector.charAt(0) === '#'){
                    this.ele = document.getElementById(selector.slice(1))
                }
                that.getValue = function(){
                    that.value = that.ele ? that.ele.innerHTML : 'no value'
                }
                that.showValue = function(){
                    return that.value
                }
            }
        }
        jQuery.fn.init.prototype = jQuery.fn
        return jQuery
    })()
    window.jQuery = window.$ = jQuery
})(window)
```
3. $("").html()\text()\val():读取选定元素的内容
- html()用来读取元素的html内容（包括html标签）
- text()用来读取表单元素的纯文本内容，包括后代元素
- val()是用来读取表单元素的value值
```
<div id="app">
    <h1>标题</h1>
    <p>段落</p>
    <input type="text" id="inp" value="表单">
</div>
<script>
    $(document).ready(function () {
        var pre = $("#app"),inp = $("#inp")
        console.log("html"+pre.html())
        console.log("text"+pre.text())
        console.log("val"+ inp.val())
    })
</script>

html
    <h1>标题</h1>
    <p>段落</p>
    <input type="text" id="inp" value="表单">
text
    标题
    段落
val
    表单
```
4. $("").attr()\prop()\addclass()...:属性操作（方法的重载）
```
$("#id").attr('title') //获取title属性的值
$("#id").attr("title","jquery") //设置title的属性
```
5. $("").css():样式操作（方法的重载）
```
$("").css("color") //获取color的值
$("").css("color","red") //设置color的值为red
```
6. $("").animate(): 动画
```
$("#id").animate({
    left: 100px  //向右移动100px
})
```
7. $("").data():取出保存的数据
```
<div data-type="jfiaoif"></div>
var type = $("").data("type")
```
8. $("").on()\trigger(): 事件
9. $.ajax({}): ajax请求
10. $.deffered(): 异步变同步，类似promise的写法
11. $("").eq(0).html(): 链式调用（每个方法返回jquery对象）
12. jquery.fn.extend(object): 给jquery对象添加实例方法，就是在原型prototype添加方法， 通过extend添加的新方法，实例化的jquery对象都可以使用，
即$("").functionName();jquery.extend(object): 给jquery添加静态方法，通过$.functionName()

## jquery源码部分知识
1. jquery.fn.css()的内部关键使用的js的getComputedStyle以及getPropertyValue方法
    - getComputedStyle()的用法：可以获取当前元素所有最终使用的css属性值，返回一个
    css样式声明对象（只读）
    ```
    var style = window.getComputedStyle("元素"，"伪类")
    var style = document.getComputedStyle(...)

    var dom = document.getElementById("test")
    style = window.getComputedStyle(dom,":after")
    ```
    - getComputedStyle与style的区别：
        1. 只读与可写：getComputedStyle只读，element.style可读可写
        2. 获取的对象范围：getComputedStyle方法获取的是最终应用在元素上的所有css属性对象（即使没有css代码，也会把默认的祖宗十八代都
        显示出来）；而lement.style只能获取元素style属性中的css样式
    - getComputedStyle与defaultView
        如果查看jquery源码，会发现css()使用的是document.defaultView.getComputedStyle;
        实际上defaultView更本没有必要，getComputedStyle本身就存在于window对象
    - ie中使用element.currentStyle,功能与getComputeStyle相似
    - getPropertyValue方法（与getAttribute()类似）可以获取css样式申明对象上的属性值（直接属性名称，ie9+浏览器都支持），如果不使用getPropertyValue方法，直接使用键值访问
    ```
    var style = document.getComputedStyle(dom,null)
    //以下三种写法是等价的
    style.getPropertyValue("float")
    style.float
    style.getAttribute("float")
    ```
    - currentStyle: 此方法为ie特有（正确的获取style方法）
    ```
    function getStyle(ele,attr) {
        if (window.getComputedStyle){
            return window.getComputedStyle(ele,false)[attr]
        }else{
            return ele.currentStyle[attr]
        }
    }
    ```

2. jquery插件的写法
2.1 原型方法
    - $.fn.functionName = function([options]){};一次一个函数
    - $.fn.extend({functionName:function(){},..});一次声明多个函数
2.2 静态方法
    - $.extend(defaults,options);其中default为默认设置，options为传入的参数；
    这个函数的作用是用后面的参数与第一个参数进行合并然后返回它的值
    ```
    (function($){
        $.fn.color = function(options){
            var defaults = {color: "blue",size:"30px"}
            console.log($.extend(defaults,options))
            console.log(defaults)
            console.log(options)
            //return的作用是支持链式调用
            return $(this).each(function(){
                $(this).css({"color": options.color})
                $(this).css({"font-size": options.size})
            })
        }
    })(window.jQuery)
    ```
    - $.extend({},defaults,options),不会导致defaults被代替，所以一般选择使用这个
    ```
    var defaults = {name:"jaksdlfj"},options = {age: 23}
    console.log($.extend(defaults,options))
    console.log(defaults)
    console.log(options)
    VM308:2 {name: "jaksdlfj", age: 23}
    VM308:3 {name: "jaksdlfj", age: 23}
    VM308:4 {age: 23}
    undefined
    var defaults = {name:"jaksdlfj"},options = {age: 23}
    console.log($.extend({},defaults,options))
    console.log(defaults)
    console.log(options)
    VM311:2 {name: "jaksdlfj", age: 23}
    VM311:3 {name: "jaksdlfj"}
    VM311:4 {age: 23}
    ```

## jquery的一些方法
1. 判断class和移除class
    - 是否包含某个class: hasClass(),结果为true或者false
    ```
    $jq.hasClass("alkhfdas")
    ```
    - 是否包含某些class: is(),可以同时包含多个class(注意写法),结果为true或者false
    ```
    $jq.is(".func,.shun")
    ```
    - 添加class: addClass()
    ```
    $jq.addClass("junm")
    ```
    - 删除class: removeClass()
    ```
    $jq.removeClass("test")
    ```
2. $.each()的用法，因为.forEach()不支持ie8
    - 使用forEach()
    ```
    var arr = [1,3,4]
    arr.forEach(function(index,value){
        ..
    })
    ```
    - 使用$.each()(注意：当是jquery对象类数组，value变成了原生js对象，使用的时候需要转化成jquery对象)
    ```
    var $list = $(".class")
    $.each($list,function(index,value){
        var $val = $(value)//将原生js对象转化成jquery对象
        $val.hasClass("test")
        ..
    })
    ```
    附录：$.each()源码
    ```
    var an = {
                isArraylike: function (obj) {
                    return obj instanceof Array
                },
                each: function(obj,callback,args){
                    var value,i = 0,length = obj.length,isArray = this.isArraylike(obj)//判断是不是数组
                    if(args){
                        if(isArray){//数组
                            for(;i < length; i++){
                                value = callback.apply(obj[i],args)
                                if(value === false){
                                    break;
                                }
                            }
                        }else{//非数组
                            for(i in obj){//遍历对象
                                value = callback.apply(obj[i],args)
                                if(value === false){
                                    break
                                }
                            }
                        }
                    }else{
                        if(isArray){
                            for(;i < length;i++){
                                value = callback.call(obj[i],i,obj[i]) //相当于callback(i,obj[i])
                                if(value === false){
                                    break
                                }
                            }
                        }else{
                            for(i in obj){
                                value = callback.call(obj[i],i,obj[i])
                                if(value === false){
                                    break
                                }
                            }
                        }
                    }
                    return obj
                }
            }

            var arr = [1,2,3,4]
            an.each(arr,function (index,val) {
                console.log(index+" value is:"+val)
            })
    ```
3. $.extend({},obj1,obj2)与$.extend(true,{},obj1,obj2)
$.extend({},obj1,obj2)是浅合并而$.extend(true,{},obj1,obj2)是深合并，迭代(recursively)
一般建议使用后者
```
var obj1 = {
    apple:0,
    banana: {weight: 52,price: 100},
    cherry: 97
}
var obj2 = {
    banana: {price: 200},
    durian: 100
}
$.extend(obj1,obj2) //{apple:0,banana:{price:200},cherry:97,durian:100}
$.extend(true,ibj1,obj2) //{apple:0,banana:{weight: 52,price:200},cherry:97,durian:100}
```
4. $().html("")与$().empty()对比
html("")的内部实现是利用innerHTML = ""来实现的；而empty("")首先给后代元素移除绑定、清除jquery给dom的cache，
然后循环removeFirstChild
```
//empty源码
empty: function(){
    var elem,
        i = 0;
    for(;(elem = this[i]) != null;i++){
        if(elem.nodeType === 1){
            //循环清除Data数据
            jQuery.cleanData(getAll(elem,false))
        }

        //移除child
        while( elem.firstChild ){
            elem.removeChild( elem.firstChild )
        }
        if(elem.options && jQuery.nodeName( elem,"select" )){
            elem.options.length = 0
        }
    }

    return this
}

```
5. 如何比较两个jQuery对象是否相等
将jQuery对象转化成原生的dom对象,或者使用$("").is()方法
```
var $obj = $(".num"),$obj1 = $(".num1")
var node = $obj.get(0),
    node = $obj[0]
$obj.is($obj1)

<div class="duan"></div>
$(".duan").is("div")
```


## 推荐jquery的相关插件（冷门但是好用的）
1. loading插件:jquery.showLoading.js
```
// 1. 声明想要用来遮罩的容器
<div id="loading"></div>
// 2. js中调用showLoading()和hideLoading()方法
jQuery("#loading").showLoading()
jQuery("#loading").hideLoading()
// 3. 修改Loading的动画图标，可以修改为自己的gif动画
.loading-indicator{
    height: 80px;
    width: 80px;
    background: url("/img/loading.gif");
    background-repeat: no-repeat;
    background-position: center center;
}
```
2. 消息提示: jquery.toast.js
3. 前端模版: handlebar.js
```
<div class="person">
    <p>姓名：{{name}}</p>
    <p>出生地：{{home}}</p>
    <p>职业：{{job}}</p>
</div>
<div class="wrap"></div>
var data = {
    name: "peng",
    home: "china",
    job: "teacher"
}
var tmpl = $(".person").html()
var compiled = Handlerbars.compile(tmpl)
var result = compiled(tmpl)
$(".wrap").html(result)
```
