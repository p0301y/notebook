1. meta标签的http-equiv属性详解
http-equiv顾名思义，相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确和精确的
显示类容，与之相对应的属性值是content，content中的内容其实就是各个参数的变量值
    - expires(期限): 可以用于设定网页的到期时间，一旦网页过期，必须到服务器上重新传输
    ```
    <meta http-equiv="expires" content="wed,20 jun 2007 22:33:00 GM">
    ```
    - pragma(cache模式): 是用于设定禁止浏览器从本地机的缓存中调阅页面内容，设定后一旦离开页面就无法从
    cache中在调出用法:
    ```
    <meat http-equiv="pragma" content="no-cache">
    ```
    - refresh(刷新):自动定时刷新，指定新的页面
    ```
    <meta http-equiv="Refresh" content="6;URL=http://www.net.cn"> //6后自动跳转
    ```
    - set-cookie(cookie设定): 如果网页过期，那么存盘的cookie将被删除
    ```
    <meta http-equiv="Set-Cookie" content="cookievalue=xxx,expires=Wednesday,20-jun-2007 22:33:00 GTM;path=/">
    ```
    - window-target(显示窗口的设定):强制页面以独立页面显示，用来防止别人在框架里调用自己的页面
    ```
    <meta http-equiv="Window-target" content="_top">
    ```
    - content-type(显示字符集的设定):设定页面使用的字符集
    ```
    <meta http-equiv="content-Type" content="text/html;charset=gb2312">
    ```
2. readonly属性在ie8下失效等问题
    - 设置unselecteble = "on"
3. input标签设置disabled和readonly的区别
    - readonly只针对input（text/password）和textarea有效，而disabled对于所有的表单元素都有效，包括select, radio, checkbox等
    - 表单元素使用GET或者post方式提交时，表单元素在使用了readonly后，值可以传递到后台；而表单元素在使用disabled后值不可以传递到后台