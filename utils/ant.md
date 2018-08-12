## 帖子
[实习面试总结]{https://segmentfault.com/a/1190000009485047}
[大佬聊前端]{https://yq.aliyun.com/articles/72028}
## 相关名词
riddle 内似codeOpen
antd
dva 应用架构，类似react
dva-cli 脚手架

## 什么是webview
webView（网络视图）能够加载显示网页，可以将其视为一个浏览器，它可以使用webkit渲染引擎加载显示
网页，所以使用h5开发需要结合webView；有andriod webView和ios webView
```
//如何判断设备的类型
var sysPlatform
var ua = navigator.userAgent.toLowerCase()
if(/iphone|ipad|ipod/.test(ua)){
    sysPlatform = "IOS"
}else if(/andriod/.test(ua)){
    sysPlatform = "ANDRIOD"
}
```
## 你想要的博客都在这里
[参考连接]{http://caibaojian.com/c/qianduan/page/4}
## webApp与hybirdApp
- webApp:在应用中内嵌网页，一般使用的开发平台是cordova，开发步骤:
```
cnpm install cordova -g
cordova create AppName
cd AppName
cnpm install
cordova platform add android
cordova platform list
cordova run android
```
- hybirdApp:三端统一开发，生成的文件是jsbundle，运行在不同的平台上，最终转化成原生；一种是vue-native,结合vue技术的weex，另一种是
结合react技术的react-native。
    - weexpack开发搭建weex项目开发hybirdApp:
    ```
    cnpm install weexpack
    weexpack create AppName
    cd AppName
    cnpm install
    weexpack platform add android
    weexpack platform list
    weexpack run android
    ```
    其中weexpack run android这条命令的时候出现下载Downloading...//services.gradle.org/distributions/gradle-2.14.1-all.zip极慢，
    解决办法：手动下载指定版本的gradle.zip到本地D:/gradle/gradle-2.14.1-all.zip,然后替换项目中 android/gradle/wrapper/gradle-wrapper.properties 的 distributionUrl ，
    即  distributionUrl=file\:///D:/gradle/gradle-2.14.1-all.zip （注意这里需要加上转义字符\）