## 两列布局
1. div的内联(inline-block)
```css
.left{
    display:inline-block;
    width: 200px;
}
.right{
    display:inline-block
}
```
2. 绝对定位(html代码的书写右边在前，左边在后，即右边先加载)【float也是可以的，脱离文档流】
```css
.right{
    width:100%
    margin-left: 200px
}
.left{
    position:absolute //脱离文档流，相对于父级非static元素
    width:200px
}
```
3. flex(flex-grow: 1)
4. calc()计算
5. 负margin（html代码的书写右边在前，左边在后，即右边先加载）【这种方式比较麻烦】
```html
<div class="leftWrap">
    <div class="left">就fda开始了</div>
</div>
<div class="right">hello</div>
<style>
    .leftWrap{
        width: 100%;
        float: left;
    }
    .left{
        /*width: 100%;*/
        background-color:#2b2b2b;
        margin-left: 200px;
        height: 100px;
    }
    .right{
        float: left;
        background-color: #00acd6;
        width: 200px;
        height: 100px;
        margin-left: -100%;
    }
</style>
```

## 常见的伪类与伪元素
1. 伪类，顾名思义，类似于在元素上添加一个class，对目标元素进行操作
    - :active 向被激活的元素添加样式
    - :focus 向拥有键盘输入焦点的元素添加样式
    - :hover 当鼠标悬浮的时候添加样式
    - :link 向未被访问的连接添加样式
    - :visited 向一杯访问的连接添加样式
    - :first-child 向第一个子元素添加样式
    - :lang 向带有lang属性的元素添加样式
2. 伪元素，顾名思义，添加新的元素标签
    - :first-letter 向文本的第一个字母添加特殊样式
    - :first-line 向文本首行添加特殊的样式
    - :before 在元素之前添加类容
    - :after 在元素之后添加类容

## flex布局-弹性布局
任何一个元素都可以指定为flex，行内元素也可以指定为flex布局，webkit内核的浏览器
必须加上-webkit前缀
```
.box{
    display: flex;
}
.box{
    display: inline-flex;
}
.box{
    display: -webkit-flex;
    display: flex;
}
```
注意设置为flex布局之后，子元素的float、clear、vertical-align属性将失效
父元素display: flex之后成为伸缩容器，子元素（除了position: absolute和fixed）
无论是display:block或者display: inline都将称为伸缩项目；伸缩项目之间，没有inline-block
元素之间的空隙；伸缩项目自动box-sizing:border-box;
1. 采用flex布局的元素称为flex容器(flex container),简称“容器”。他的所有
子元素自动成为容器成员，称为flex项目(flex item),简称项目
2. 容器的属性
    - flex-direction: 决定主轴的方向（即项目排列的方向）
    ```
    .box{
        flex-direction: row | row-reverse | column | column-reverse
    }
    ```
    - flex-wrap: 决定容器中的项目是否换行，默认是不换行的
    ```
    .box{
        flex-wrap: nowrap | wrap | wrap-reverse
    }
    ```
    - flex-flow: 是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap
    - justify-content: 定义项目在主轴上的对齐方式
    ```
    .box{
        justify-content: flex-start | flex-end | center | space-between | space-around
    }
    ```
    - align-items: 定义项目在交叉轴上如何对齐
    ```
    .box{
        align-items: flex-start | flex-end | center | baseline | strench;
    }
    ```
    - align-content: 定义多根轴线的对齐方式
3. 项目属性
    - order: 定义项目的排列顺序，数值越小越靠前，默认值为0
    ```
    .item{
        order: <Integer>;//可以为负数
    }
    ```
    - flex-grow: 定义放大比例，默认值为0，即如果存在剩余空间，也不放大，通常用来响应式布局，填充剩余的部分
    ```
    .item{
        flex-grow: <number>;/*default 0*/
    }
    ```
    如果所有的项目flex-grow属性都为1，则它们将等分剩余的空间；如果为其他数>0,则按比例分配剩余的空间
    - flex-shrink: 定义了项目的缩小比例，默认为1，即空间不足，该项目将缩小，不想项目缩小则设置为0
    ```
    .item{
        flex-shrink: <number>; /*default 1 不能为负数*/
    }
    ```

    - flex-basis: 定义了在分配多余的空间之前，项目占据的主轴空间(main size)，浏览器根据这个属性计算主轴是否有多余的空间，它的默认值为auto，即项目的本来大小
    ```
    .item{
        flex-basis: <length> | auto; /* default auto */
    }
    ```
    它可以设置为跟width或height属性一样的值(比如350px)，则项目将占据固定空间
    - flex: 是flex-grow\flex-shrink\flex-basis的简写，默认值为0 1 auto
    - align-self: 允许单个项目与其他项目不一样的对齐方式，可以覆盖align-items属性，默认值为auto，
    表示继承了父元素的align-items属性
[参考连接]{http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html}

## css选择器
- 基本判断：行间 > 内部 > 外部样式：ID > class > 元素
- 选择器类型：id，class，标签，通用，属性，伪类，伪元素，子选择器以及相邻选择器
- 权重判断
    - 第一等：代表样式内联，如：style=""，权值为1000
    - 第二等：代表id选择器，如：#content,权值0100
    - 第三等：代表类，伪类和属性选择器，如：.content,权值0010
    - 第四等：代表类型选择器和伪元素选择器，如div p，权值0001
    - 通配符、子选择器、相邻选择器，如*、>、+,权值为0000
    - 继承的样式没有权值

## 居中问题
1. position: absolute;和position: fixed;如何居中
```
{
    position: absolute;/fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
}
```

## css3中的长和宽的单位(视口单位)
1. vw: 1vw等于视口宽度的1%
2. vh: 1vh等于视口高度的1%
3. vmin: 选取vw和vh中最小的那个
4. vmax: 选取vw和vh中最大的那个
注意：视口单位区别于%单位，视口单位是依赖于视口的尺寸，根据视口尺寸的百分比来定义；而%单位则是依赖于元素的祖先元素
一般用法：仅使用vw作为css单位
优化用法：搭配vw和rem
```
//优化用法
// rem 单位换算：定为 75px 只是方便运算，750px-75px、640-64px、1080px-108px，如此类推
$vm_fontsize: 75; // iPhone 6尺寸的根元素大小基准值
@function rem($px) {
     @return ($px / $vm_fontsize ) * 1rem;
}
// 根元素大小使用 vw 单位
$vm_design: 750;
html {
    font-size: ($vm_fontsize / ($vm_design / 2)) * 100vw;
    // 同时，通过Media Queries 限制根元素最大最小值
    @media screen and (max-width: 320px) {
        font-size: 64px;
    }
    @media screen and (min-width: 540px) {
        font-size: 108px;
    }
}
// body 也增加最大最小宽度限制，避免默认100%宽度的 block 元素跟随 body 而过大过小
body {
    max-width: 540px;
    min-width: 320px;
}
```

### 居中问题的解决方案

- 水平居中
```$xslt
<div class=parent>
    <child class="child">
    </div>
</div>
```
1.inline-block + text-align
```$xslt
.child{
    display:inline-block;
    }
.parent{
    text-align:center;
}
```
2.table + margin
```$xslt
.child{
    display:table;
    margin: 0 auto;
}
```
3.absolute + transform
```$xslt
.parent{
    position: relative
}
.child{
    position: absolute;
    left:50%;
    transform: translateX(-50%)
}
```
4.flex + justify-content
```$xslt
.parent{
    display:flex;
    justify-content:center;
}
or
.parent{
    display: flex;
}
.child{
    margin: 0 auto
}
```

- 垂直居中
1. table-cell + vertical-align
```$xslt
.parent{
    display: table-cell;
    vertical-align:middle;
}
```
2.absolute + transform
```$xslt
.parent{
    position: relative;
}
.child{
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
```
3.flex + align-items
```$xslt
.parent{
    display:flex;
    align-items: center;
}
```

### 多列布局
- 一列定宽，一列自适应
```$xslt
div.parent
    div.left
    div.right
```
1.float + margin
```$xslt
.left{
    float: left;
    width: 100px;
}
.right{
    margin-left:100px;
}
```
2.float + overflow
```
.left{
    float: left;
    width:100px;
}
.right{
    overflow:hidden;
}
```
3.table
```$xslt
.parent{
    display:table;
    width:100%
    table-layout:fixed;
}
.left,.right{
    display:table-cell;
}
.left:{
    width:100%;
}
```
4.flex
```$xslt
.parent{
    display:flex;
}
.left{
    width:100px;
}
.right{
    flex:1;
}
```

- 不定宽度 + 自适应
1.float + overflow
```$xslt
.left{
    float:left;
}
.right{
    overflow:hidden;
}
```
2.table
```$xslt
.parent{
    display:table;
    width: 100%;
}
.left,.right{
    display:table-cell;
}
.left{
    width: 0.1%;
}
```
3.flex
```$xslt
.parent:{
    diplay:flex;
}
.right{
    flex:1;
}
```

- 等宽布局
1.25%计算
2.table + table-layout:fixed
3.flex的分配因子

- 左右等高布局（现实中经常需要的）
1.table
```$xslt
.parent{
    display:table;
    width:100px;
    table-layout: fixed;
}
.left,.right{
    display:table-cell;
}
.left{
    width:100px;
}
```
2.flex
```$xslt
.parent{
    display:flex;
}
.left{
    width:100px;
}
.right{
    flex:1;
}
```

## 移动端的适配方案
1. 理解几个基本的概念
    - 设备像素：物理像素，又叫分辨率，单位是pt
    - 屏幕尺寸：屏幕对角线的长度
    - 屏幕像素密度（ppi）：根据物理像素和屏幕尺寸计算出来的，原则上ppi越高越好，因为图像会更加清晰
    - css像素：又称为是逻辑像素， 是css和js中使用的一个抽象的概
    - 布局视口（layout viewport）：可以用来布局的视口大小，包括滚动条
    - 视觉视口(visual viewport)：可以看作是手机屏幕这么大的窗口
    ```
    <meta name='viewport' content='width=device-width'>  //将layout viewport设置为屏幕的宽度
    <meta name='viewport' content='width=640'>  //将layout viewport宽度设置为640px(逻辑像素)
    <meta name='viewport' content='inital-scale=0.5'>  //visusl viewport的0.5倍是屏幕宽度，
    所以visual viewport的宽度就是屏幕的2倍了，此时布局视口的宽度也是屏幕宽度的2倍
    ```
    - 总结： width=x设置布局视口，initial-scale=y设置视觉视口；
    如果只设置布局视口和视觉视口中的一个，那么另一个也是同样的宽度；
    布局视口的宽度始终大于等于视觉视口
    ```
    <meta name='viewport' content='width=device-width,initial-scale=1.0'>
    // layout viewport浏览器窗口，设置为屏幕宽度，visual viewport设置为屏幕宽度，不放缩，屏幕多宽就显示多少像素，也就是所谓的完美视口
    ```
2. 移动端的适配方案
    - 固定高度，宽度自适应: 随着屏幕宽度的变化，页面也会跟着变化，效果和pc页面的流体布局差不多
    ```css
    <meta name='viewpport' content='width=device-width,initial-scale=1'>
    ```
    - 固定宽度，viewport缩放：根据页面的宽度，计算initial-scale的值，来动态生成viewport，
    - 使用rem或者vw/vh作宽度：其中使用rem的时候，根据屏幕的宽度，动态设置rem对应的px值，即html{font-size: x}
    - 使用阿里的flexible
3. 总结
    - 物理像素：手机屏幕上最小的物理显示单元
    - css像素（设备独立像素）：一种度量单位
    - 设备像素比（dpr）： 物理像素 / css像素，比如一个移动设备的物理像素为640px,他的屏幕宽度为320px，则dpr=2，可以通过window.devicePixelRatio获取dpr
    - 举例：一个交互稿为640px宽度，设备A、B的宽度320px，A的dpr为1，B的dpr为2，那么如何适配这两种屏幕呢？  
    1. 设置html中font-size的值，即rem的值(rem = document.documentElement.clientWidth * dpr / 10)：A(320 / 10) = 32px  B(320*2 / 10) = 64px
    2. 设置initial-scale的值，即缩放比例(scale = 1 / dpr)
    3. 设置data-dpr(用于css hack使用)： data-dpr = dpr
    4. 字体的大小设置(任何手机屏幕的字体大小都要统一)： font-size: 16px; [data-dpr='2'] input { font-size: 32px; }
    ```javascript
    var dpr, rem, scale
    var docEl = document.documentElement
    var fontEl = document.createElement('style')
    var metaEl = document.querySelector('meta[name="viewport"]')
    
    dpr = window.devicePixelRatio || 1
    rem = docEl.clientWidth * dpr / 10
    scale = 1 / dpr
    
    // 设置viewport，进行放缩，达到高清效果
    metaEl.setAttribute('content', `width=${dpr*docEl.clientWidth}`, `initial-scale=${scale}`)
    
    // 设置data-dpr属性，留作的css hack之用
    docEl.setAttribute('data-dpr', dpr)
    
    // 动态写入样式
    docEl.firstElementChild.appendChild(fontEl)
    fontEl.innerHTML = `html{font-size: ${rem}px !important;`
    
    // 给js调用的，某一dpr下rem与px之间的转换函数
    window.rem2px = function(v){
       v = parseFloat(v)
       return v * rem
    }
    
    window.dpr = dpr
    window.rem = rem
    ```
    