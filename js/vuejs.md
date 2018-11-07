## 理解vuejs
传统的方法：修改某个对象的属性，就选取这个属性对应的html上的dom，如果添加一个对象
或者说是node节点，使用的是append方法
vuejs的思路是不再需要操作dom了，你只要操作数据对象就可以了，对应的html会自动更新
，所以使用vue的时候，基本可以隔离dom操作，代码中的大部分操作对象或者数组

总结：“数据驱动的组件系统”，“dom操作封装在指令中”

## vuejs的双向绑定
访问器属性（es5 的新增，所以不支持ie8）：访问器属性是一种特殊的属性，他不能直接在对象中设置
，而必须通过Object.defineProperty()单独定义：
```javascript
Object.defineProperty(obj,prop,descriptor)
var obj = {}
Object.defineProperty(obj,"key",{
    enumerable: false,
    configurable: false,
    writable: false,
    value: "static",
    set: function(val){
        this.key = val
    },
    get: function(){
        return this.key
    }
})
```
最重要的是set方法：在改变属性的值的时候，需要通过set函数

- view数据改变，以input为例子，添加change事件，值改变的时候设置obj的prop
- obj的prop中的set方法添加一个watcher，值改变触发watcher【发布者】
- 所有有关prop的dom用都是一个对象【订阅者】，包含更改prop值的方法，将所有对象添加到dep【主题对象】
- watcher通过主题对象使所有的观察者更新数据

## vuejs的自定义指令
在vue2.0中，代码的抽象和重用的主要形式是以组件的形式，但是可能在某些情况下，还是需要
对普通元素进行一些底层的dom访问，这是自定义指令仍然使用的场景之处，可以说是“数据驱动视图”的
一种有效补充或者说是扩展，不仅可用于定义任何的dom操作，并且是可以复用的

自定义指令的方法是：Vue.directive("focus",{各种钩子函数})
钩子函数（全部可选）：
bind: 在指令第一次绑定到元素调用，只会调用一次
unbind: 在指令从元素上解除绑定时调用，只会调用一次
inserted: 在已绑定的元素插入到父节点时调用
update:
componentUpdate:

钩子函数的参数：
el: 指令绑定的元素，可以用于直接的dom操作
binding: 一个对象（name,value,oldValue,expression,arg,modifiers）
vnode: 由vue编译器生成的虚拟dom
oldVnode: 之前的虚拟dom

## vue2.0中的虚拟dom
"watcher"是来决定你要不要来更新这个dom，而虚拟DOM是用来找出怎么以最小的代价来更新
这个DOM，其实有了虚拟dom，更大的意义是更好的支持了服务器端渲染；虚拟dom对应的是真实的
dom，使用document.CreateElement和document.CreateTextNode创建的就是真实节点；在更新的时候
通过对比虚拟dom和真实dom来进行必要的dom更新，减少不必要的更新，从而优化性能。其中
虚拟树与真实树的比较涉及到diff算法[参考]{https://github.com/aooy/blog/issues/2}

1. 为什么需要使用虚拟dom？
    - DOM是很慢的，其元素非常大，页面的性能问题很少是因为js引起的，大部分是由dom操作引起的，
    如果对前端进行抽象的话，主要是维护状态和更新视图，而更新视图必须要操作dom；在jquery出现以前
    直接操作dom结构，这种方法复杂度高，兼容性差，jquery强大的选择器以及高度封装的API，解决了兼容性
    问题，同时dom操作也变得简单
    - 接下来，mvvm框架应运而生，使用数据双向绑定，使得我们完全不用操作dom了，更新视图状态会自动更新，
    更新了视图数据状态也会自动更新，可以说大大提升了开发效率，但是大量的事件绑定在复杂场景下的执行性能
    堪忧，有没有一种提高开发效率和执行效率（更新视图）：虚拟dom的诞生
2. 理解虚拟dom？
    - 核心思想：对复杂的文档DOM结构，提供一种方便的工具，进行最小化操作dom：一方面提供方便的工具，使得
    开发效率得到保证；另一方面保证最小的dom操作，使得执行效率得到保证
    - js操作dom：dom很慢，而js很快，用js对象可以很容易地表示dom节点。dom节点包括标签、属性和子节点，通过
    VElement表示：
    ```
    //虚拟dom，参数分别为标签名、属性对象、子DOM列表
    var VElement = function(tagName,props,children){
        //保证只能通过如下方式调用：new VElement
        if(!(this instanceof VElement)){
            return new VElement(tagName,props,children)
        }

        //可以通过只传递tagName和children参数
        if(util.isArray(props)){
            children = props
            props = {}
        }

        //设置虚拟dom的相关属性
        this.tagName = tagName
        this.props = props || {}
        this.children = children || []
        this.key = props ? props.key : void 666
        var count = 0
        util.each(this.children,function(child,i){
            if(child instanceof VElement){
                count += child.count
            }else{
                children[i] = '' + child
            }
            count++
        })
        this.count = count
    }
    ```
    通过VElement，我们可以很简单地用javascript表示dom结构，比如：
    ```
    var dom = velement('div',{'id':'container'},[
        velement('h1',{style: 'color:red'},['item #1'])
    ])
    //表示的dom结构
    <div id="container">
        <h1 style="color:red">item #1</h1>
    </div>
    ```
    同样我们可以很方便地根据虚拟dom树构建出真实的DOM树，具体思路：根据虚拟dom节点的属性和子节点递归构建真实的dom
    树
    ```
    VElement.prototype.render = function(){
        //创建标签
        var el = document.createElement(this.tagName)
        //设置标签属性
        var props = this.props
        for(var propName in props){
            var propValue = props[propName]
            util.setAttr(el,propName,propValue)
        }

        //依次创建子节点的标签
        util.each(this.children,function(child){
            //如果子节点仍然为velement，则递归创建子节点，否则直接创建文本类型节点
            var childEl = (child instanceof VElement) ? child.render() : document.createTextNode(child)
            el.appendChild(childEl)
        })

        return el
    }
    //对一个虚拟的DOM对象VElement，调用其原型的render方法，就可以产生一颗真实的DOM树
    vdom.render()
    //既然我们可以用js对象表示dom结构，那么当数据状态发生变化而需要改变的dom结构时，先通过js对象js对象表示
    的虚拟dom计算出实际dom需要做的最小变动，然后实际dom，从而避免了粗放式的dom操作带来的性能问题
    ```
比较两颗虚拟dom树的差异(old virtual DOM 和 new virtual DOM)使用diff算法对真实dom最小修改

## mvc到mvvm
1. mvc
    - view ui 布局，展示数据
    - Model 管理数据
    - Controller 响应用户操作，并将Model更新到view上

    随着h5的发展，人们更希望使用h5开发的应用能和Native媲美，或者接近与原生App的体验效果，于是前端应用的复杂程度已不同往日，此时前端开发就暴露出了三个痛点：
    开发者在代码中大量调用相同的DOM API，处理繁琐操作冗余，使得代码难以维护；大量的dom操作使得页面渲染性能降低，加载速度减慢，影响用户体验；当Model频繁发生
    变化，开发者需要手动更新到View，当用户的操作导致Model发生变化，开发者同样需要将变化的数据同步到Model中，这样工作很繁杂。
    其实，jquery的出现就是为了前端更加简洁的操作dom，同时封装了对不同浏览器的兼容性写法
2. MVVM
    - model 数据模型
    - view 视图模型
    - viewModel 同步view和model的对象
    在mvvm架构下，view和model之间并没有直接的联系，而是通过viewModel进行交互，model和viewModel之间的交互是双向的，因此view数据变化会同步到model，而model数据的变化
    也会立刻反映到view上；viewModel通过双向数据绑定把view层和model层连接起来，而view和model之间的同步工作是自动完成的，因此开发者关注业务逻辑，不需要动手操作dom，
    不需要关注数据状态的同步问题，复杂的数据状态维护完全有mvvm来统一管理
    ![](../image/3.png)
    vue.js是采用object.defineProperty的getter和setter并结合观察者模式来实现数据绑定。把一个普通javascript对象传给Vue实例来作为它的data选项时，vue将遍历它的属性，
    用Object.defineProperty将它们转化为setter\getter，用户看不到getter和setter，但是在内部它们让vue追踪依赖，在属性被访问和修改时通知变化，具体分析如下：
        - observer数据监听器，能够对数据对象的所有属性进行监听，如果有变动就可以拿到最新值并通知订阅者，内部采用object.defineProperty的setter和getter来实现
        - compile指令解析器，它的作用是对每个元素节点的指令进行扫描和解析，根据指令模版替换数据，以及绑定相应的更新函数
        - watcher订阅者，作为连接observer和compile的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应的回调函数
        - dep消息订阅器（主题对象），内部维护一个数组，用来收集watcher订阅者，数据变动触发notify函数，再遍历订阅者并且触发订阅者的update方法

        从上图可以看出，当执行new Vue()时，vue进入初始化阶段，一方面vue会遍历data选项中的属性，并用object.defineProperty将他们转化为setter\getter,
        实现数据变化监听功能，另一方面，vue的指令编译器compile对元素节点的指令进行扫描和解析；初始化视图，并订阅watcher来更新视图，此时watcher会将自己添加到消
        息订阅器dep中，初始化完毕；
        当数据发生变化时，observer中的setter方法被触发，setter会立即调用dep.notify()，dep开始遍历所有的订阅者，并且调用订阅者的update方法，订阅者收到通知后会进行相应的
        更新

## vue双向绑定的简单实现
需要从三个方面去实现
- 输入框以及文本节点与data中的数据绑定（需要对dom进行编译：DocumentFragment）
- 输入框内容变化时，data中的数据同步变化，即view => model的变化
- data中的数据变化时，文本节点的内容同步变化，即model => view的变化
说明：订阅发布模式：发布者发出通知；主题对象收到通知并推送给订阅者；订阅者执行相应的操作
```html
<div id="app">
    <input type="text" v-model="text">
    {{ text }}
</div>
<script>
    var vm = new Vue({
        el: "app",
        data: {
            text: 'hello world'
        }
    })
</script>
```
```javascript
//数据初始化绑定,挟持dom绑定
function compile(node,vm){
    var reg = /\{\{(.*)\}\}/
    //节点类型为元素
    if(node.nodeType === 1){
        var attr = node.attributes
        //解析属性
        for(var i=0;i<attr.length;i++){
            if(attr[i].nodeName == 'v-model'){
                var name = attr[i].nodeValue  //获取v-model绑定的属性名
                //添加事件view => model
                node.addEventListener('input',function(e){
                    vm[name] = e.target.value
                })
                node.value = vm.data[name] //将data的值赋值给node
                node.removeAttribute('v-model')
            }
        }
    }
    //节点类型为text
    if(node.nodeType === 3){
        if(reg.test(node.nodeValue)){
            var name = RegExp.$1 //获取匹配的字符串
            name = name.trim()
            node.nodeValue = vm.data[name] //将data的值赋值给node
        }
    }
}
function nodeToFragment(node,vm){
    var flag = document.createDocumentFragment()
    var child
    while(child = node.firstChild){
        compile(child,vm)
        flag.appendChild(child) //将节点劫持到文本片段中
    }

    return flag
}

//响应式数据绑定
function defineReactive(obj,key,val){
    var dep = new Dep()
    Object.defineProperty(obj,key,{
        get: function(){
            //添加订阅者watcher到主题对性爱那个dep
            if(Dep.target) dep.addSub(Dep.target)
            return val
        },
        set: function(newVal){
            if(newVal == val) return
            val = newVal
            //作为发布者通知
            dep.notify()
        }
    })
}


function init(obj,vm){
    Object.keys(obj).forEach(function (key){
        defineReactive(vm,key,obj[key])
    })
}

//观察者,在html编译过程中，每个与data关联的节点生成一个watcher
function Watcher(vm,node,name){
    Dep.target = this
    this.name = name
    this.node = node
    this.vm = vm
    this.update()
    Dep.target = null
}
Watcher.prototype = {
    constructor: Watcher,
    update: function(){
        this.get()
        this.node.nodeValue = this.value
    },
    get: function(){
        this.value = this.vm[this.name]
    }
}

//主题对象
function Dep(){
    this.subs = []
}
Dep.prototype = {
    construcotr: Dep,
    addSub: function(sub){
        this,subs.push(sub)
    },
    notify: function(){
        this.subs.forEach(function(sub){
            sub.update()
        })
    }
}


//vue对象
function vue(options){
    this.data = options.data
    var data = this.data

    //初始化数据将数据转化成setter/getter
    init(data,this)

    var id = options.el
    var dom = nodeToFragment(document.getElementById(id),this)
    //编译完成后，将dom返回到app中
    document.getElementById(id).appendChild(dom)
}

var vm = new Vue({
    el: 'app',
    data: {
        text: 'hello world'
    }
})
```

## vue组件按需引用，vue-router懒加载，vue打包优化，加载动画
结合vue的异步组件和webpack的code splitting feature,轻松实现路由组件的懒加载
- 把路由对应的组件对应成异步组件
```
const Foo = resolve => {
    require.ensure(['./Foo.vue'],() => {
        resolve(require('./Foo.vue'))
    })
}
//另一种AMD分隔的require
const Foo = resolve => require(['./Foo.vue'],resolve)
//不需要任何改变路由配置，跟之前一样使用Foo
const router = new VueRouter({
    routes: [
        {path: '/foo',component: Foo}
    ]
})

//另一种写法
const router = new Router({
    routes: [
        {
            path: "/foo",
            name: 'foo',
            component(resolve){
                require.ensure(['./components/foo.vue'],() => {
                    resolve(require('./components/foo.vue'))
                })
            }
        }
    ]
})
```
- 把组件按组分块：有时候我们想把某个路由下的所有组件都打包在同个异步chunk中，只需要给chunk命名，提供require.ensure第三个参数作为chunk的名称
```
const Foo = r => require.ensure([],() => r(require('./Foo.vue')),'group-foo')
const Bar = r => require.ensure([],() => r(require('./Bar.vue')),'group-foo')
const Baz = r => require.ensure([],() => r(require('./Baz.vue')),'group-foo')
```
webpack将相同chunk下的所有异步模块打包到一个异步模块里面----这也意味着我们无需明确列出require.ensure的依赖（传空数组就行）
如果想在组件加载的时候可以有loading提示也同样很简单，这里以mint-ui为例（vue的前端组件库）
```
//npm i mint-ui -s
import { indicator } from 'mint-ui'
const Foo = resolve => {
    Indicator.open()
    require.ensure(['./Foo.vue'],() => {
        resolve(require('./Foo.vue'))
        indicator.close()
    })
}
//这样就可以实现组件在异步加载的时候显示loading
```
 [参考连接：]{http://www.cnblogs.com/coolslider/p/7074609.html}

## vue动画
vue在插入、更新或者移除dom时，提供多种不同方式的应用过度效果
包括以下工具：
    - 在css过度和动画中自动应用class
    - 可以配合使用第三方css动画库，如Animate.css
    - 在过度钩子函数中使用javascript直接操作dom
    - 可以配合使用第三方javascript动画库，如Velocity.js
1. 单元素/组件的过度
vue提供了transition的封装组件，在下列情形中，可以给任何元素和组件添加entering/leaving过度
    - 条件渲染（使用v-if）
    - 条件展示（使用v-show）
    - 动态组件
    - 组件根节点
[不明白就看：]{http://www.jb51.net/article/108616.htm}
[官方连接：]{https://vuefe.cn/v2/guide/transitions.html}
## vue.js中利用js的event loop的原理
vue中会异步执行dom更新，当观察到数据变化时，vue将开启一个队列，并缓冲在同一事件循环中发生的
所有数据变化。如果同一个watcher被触发，只会一次推入到队列中。这种在缓冲时去除重复数据对于避免不必要的
计算和dom操作非常重要。然后，在下一个的事件循环“tick”中，vue刷新队列并执行实际（已去重的）工作。vue在
内部尝试对异步队列使用原生的Promise.then和MutationObserver，如果执行环境不支持，会采用setTimeout（fn,0）
代替
```
//而当我门希望在数据更新之后执行某些DOM操作，就需要使用nextTick函数添加回调
<div id="example">{{message}}</div>

var vm = new Vue({
    el: "#example",
    data: {
        message: '123'
    }
})
vm.message = 'new message'
vm.$el.textContent === 'new message'
Vue.nextTick(function(){
    vm.$el.textContent === 'new message'
})
```
在组件内使用vm.$nextTick()实例方法特别方便，因为它不需要全局Vue，并且回调函数中的this将自动绑定到vue实例
```
vue.component("example",{
    template: '<span>{{message}}</span>',
    data: function(){
        return {
            message: "没有更新"
        }
    },
    methods: {
        updateMessage: function(){
            this.message = '更新完成'
            console.log(this.$el.textContext) //没有更新
            this.$nextTick(function(){
                console.log(this.$el.textContent) //更新完成
            })
        }
    }
})
```
使用microTask来执行批次任务
```
export const nextTick = (function(){
    //需要执行的回调函数
    const callbacks = [];

    //是否处于挂起状态
    let pending = false;

    //时间函数句柄
    let timerFunc;

    //执行并且清空所有的回调列表
    function nextTickHandler(){
        pending = false
        const copies = callbacks.slice(0)
        callbacks.length = 0
        for(let i=0;i<copies.length;i++){
            copies[i]()
        }
    }
    //nextTick的回调会被加入到MicroTask队列中，这里我们主要通过原生的Promise与MutationObserver实现
    if(typeof Promise !== 'undefined' && isNative(Promise)){
        let p = Promise.resolve()
        let logError = err => {
            console.log(err)
        }

        timeFunc = () => {
            p.then(nextTickHandler).catch(logError)
            //在部分ios系统下的UIWebViews中，Promise.then可能不会被清空
            if(isIOS) setTimeout(noop)
        }
    }else if{
        typrof MutationObserver !== 'undefined' &&
        (isNative(MutationObserver) ||
        MutationObserver.toString() === '[object MutationObserverConstructor]'){
            let counter = 1
            let observer = new MutationObserver(nextTickHandler)
            let textNode = documnet.createTextNode(String(counter))
            observer.observe(textNode,{
                charactorData: true
            })
            timerFumc = () => {
                counter = (counter + 1) % 2
                textNode.data = String(counter)
            }
        }else{
            //如果都不存在，则回退使用setTimeout
            timerFunc = () => {
                setTimeout(nextTickHandler,0)
            }
        }

        return function queueNextTick(cb?:Function,ctx?:object){
            let _resolve
            callbacks.push(() => {
                if(cb){
                    try{
                        cb.call(ctx)
                    }catch(e){
                        handlerError(e,ctx,'nextTick')
                    }
                }else if(_resolve){
                    _resolve(ctx)
                }
            })if(!pending){
                pending = true
                timeFunc()
            }

            //如果没有传入回调，则表示以异步方式调用
            if(!cb && typeof Promise !== 'undefined'){
                return new Promise((resolve,reject) => {
                    _resolve = resolve
                })
            }
        }
    }
})()
```
[参考连接]{https://zhuanlan.zhihu.com/p/29116364?spm=5176.100239.blogcont205502.24.esFIuf}
## vuejs的计算属性computed和观察属性watch
1. 计算属性
- 模版里面的表达式是非常便利的，但是实际上只应用于简单的计算，因此只允许一个表达式
```
<div>
    {{ message.split('').reverse().join('') }}
</div>
//在这种情况下，模版不再简单和清晰
```
- 声明计算属性
```
<div>
    {{reverseMessage}}
</div>
<script>
    var vue = new Vue({
        el: "",
        data(){
          return {
            message: "hello"
          }
        },
        computed: {
            reverseMessage: function(){
                return this.message.split('').reverse().join('')
            }
        }
    })
</script>
```
2. watcher观察者属性,直接观察数据变化
```
<div>{{ fullName }}</div>
<script>
    var vm = new Vue({
        el: "data",
        data: {
            firstName: "Roo",
            fullName: ""
        },
        watch: {
            //firstName变化的时候，将新的firstName作为参数传入
            firstName: function(val){
                this.fullName = this.firstName + "hello"
            }
        }
    })
</script>
```
3. computed vs watch
- 理论上，computed能做的事情watch也能做，只是适用的场景不同，简单的依赖data中的数据，使用computed，需要观察data的数据变化，在变化之后进行操作，包括复杂的异步请求操作等，使用watch


## vuejs中遇到的问题
1. vuejs中使用iconfont把编码放在data中绑定到页面不会被解析，为什么？
浏览器会当作普通的字符串处理；解决方法是使用v-html绑定data，当作html去解析
2. 脏检查是什么意思？
在angular中，使用的是脏检查机制，它并不是循环定时检查数据，而是设置了一些条件（如ajax请求等），当条件
触发后，它就会执行一个检测来遍历所有的数据，对比更改了得地方，然后执行变化；这种检查不科学，而且效率
不高，有很多多余的地方，不能像vue一样精确的更新，所以称为脏检查
3. vue更新到2.0以后，就宣布不再更新vue-resource了，推荐使用axios

## vue-router的实现
1. “更新视图但不重新请求页面”是前端路由原理的核心之一，目前有两种实现方式：利用URL的hash("#");
利用history interface在HTML5中新增的方法；（vue-router中是通过mode这一参数控制路由的实现模式的）
```
const router = new VueRouter({
    mode: "history",
    routes: [...]
})
```
VueRouter对象的源码
```
export default class VueRouter{
    mode: string; //传入的字符串参数，指示history类别
    history: HashHistory | HTML5History | AbstractHistory; //实际起作用的对象属性
    fallback: boolean; //如果浏览器不支持，"history"模式回滚到"hash"模式

    constructor (option: RouterOptions = {}){
        let mode = options.mode || "hash" //默认为"hash"模式
        this.fallback = mode === 'history' && !supportsPushState
        if(this.fallback){
            mode = "hash"
        }
        if(!inBrowser){
            mode = 'abstract' //不再浏览器环境下运行需要强制为"abstract"模式
        }
        this.mode = mode

        //根据mode确定history实际的类并实例化
        switch(mode){
            case 'history':
                this.history = new HTML5History(this,options.base)
                break
            case 'hash':
                this.history = new HashHistory(this,options.base,this.fallback)
                break
            case 'abstract':
                this.history = new AbstractHistory(this,options.base)
                break
            default:
                if(process.env.NODE_ENV !== 'production'){
                    assert(false,'invalid mode: ${mode}')
                }
        }
    }

    init(app:any /* Vue component instance */){
        const history = this.history

        //根据history的类别执行相应的初始化和监听
        if(history instanceof HTML5History){
            history.transitionTo(history.getCurrentLocation())
        }else if(history instanceof HashHistory){
            const setupHashListener = () => {
                history.setupListeners()
            }
            history.transtionTo(
                history.getCurrentLocation(),
                setupHashListener,
                setupHashListener
            )
        }

        history.listen(route => {
            this.apps.forEach((app) => {
                app._route = route
            })
        })
    }

    //VueRouter类暴露的以下方法实际是调用具体history对象的方法（代理封装）
    push(location: RawLocation,onComplete?:Function,onAbort?:Function){
        this.history.push(loaction,onCompiete,onAbort)
    }

    replace(loaction: RawLoaction,onComplete?: Function,onAbort?: Function){
        this.history.replace(loaction,onComplete,onAbort)
    }
}
```

- HashHistory原理：#符号本身以及它后面的字符称之为hash，可以通过window.location.hash属性读取，它有的特征：
    - hash虽然出现在URL中，但不会被包括在HTTP请求中，他是用来指导浏览器动作的（或者说是存储数据），对服务器端完全
    无用，因此，改变hash不会重新加载页面
    - 可以为hash的改变监听事件
    ```
    window.addEventListener("hashChange",funcRef,false)
    ```
    - 每一次改变hash（window.location.hash），都会在浏览器的访问历史中增加一条记录
- HTML5History: history interface是浏览器历史记录栈提供的接口，通过back(),forword(),go()等方法，
我们可以读取浏览器历史记录栈的信息，进行各种跳转操作。从HTML5增加了pushState()和replaceState()可以对浏览器
历史记录栈进行修改，换句话说，按照自己的想法存储url：
```
window.history.pushState(stateObject,title,URL)
window.history.replaceState(stateObject,title,URL)
```
$route和$router的区别：$router是一个vue-router实例对象，包含路由对象与属性；$route是一个跳转的路由对象，每一个路由都会有一个route对象，是一个局部对象，可以获取对应的name,path,params,query等，可以通过vue-devtools看到每个路由对象的不同。
 - stateObject: 当浏览器跳转到新的状态时，将触发popState事件，该事件将携带这个stateObject参数的副本
 - title: 所添加记录的标题
 - url: 添加记录的url
参考连接：[参考连接]{https://zhuanlan.zhihu.com/p/27588422}

## vue插件的写法
1. 插件一般的导入形式(选择哪种方式，根据需要决定)
```
es6
import vuePayKeyboard from 'vue-pay-keyboard'

//通过require导入
var vuePayKeyboard = require('vuePayKeyboard')

//通过use挂载，全局注册
Vue.use(vuePayKayboard)

//或者直接导入js文件
<script src="./dist/vue-pay-keyboard.js"></script>
```
2. vue插件规范
```
const plug = { //定义一个对象
    install(Vue,options){ //需要拥有一个install方法

    }
}
//导出这个对象
export default plug
```
3. vue.use源码分析
```
Vue.use = function(plugin){
    if (plugin.installed){
        return
    }

    var args = toArray(arguments,1)
    args.unshift(this)
    if(typeof plugin.install === 'function'){
        plugin.install.apply(plugin,args)
    }else if(typeof plugin === 'function'){
        plugin.apply(null,args)
    }

    plugin.installed = true
    return this
}
```
4. 书写插件
```
//1. 添加全局方法或属性
Vue.myGlobelMethod = function(){

}
//2.添加全局资源[指令或者是过滤器等]
Vue.directive("my-directive",{
    bind(el,binding,vnode,oldVnode){
    }
})
Vue.filter("formatTime",function(){
})
//3.注入组件
Vue.mixin({
    created: function(){
    }
})
//4. 添加实例方法
Vue.prototype.$myMethod = function(options){
    //逻辑
}
```
5. vue.js的一般项目目录
```
.
├── App.vue             # 项目如何组件
├── api                 # 存放接口相关文件
├── assets              # 存放项目资源文件，比如图片
├── components          # 存放通用组件
├── directive           # 存放全局自定义指令
├── filters             # 存放全局过滤器
├── main.js             # 项目入口文件
├── mock                # mock数据
├── router              # 路由
├── store               # 状态管理
├── styles              # 样式文件
├── utils               # 存放工具函数
└── views               # 存放视图类组件

```
