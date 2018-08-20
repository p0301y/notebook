## 道交系统
- 技术选型
    avalon.js + jquery.js + pintuer.css + 前端模板
- 单页面系统的路由的实现
- 前端模板的使用

## 易起学小程序开发和后台管理系统
- 技术选型  
wepy + redux + axios（小程序）   
vue + elment UI（后台管理web）
- 问题与解决方案   
redux的书写方式：以tree节点的方式书写   
页面上传word等文件：web-view内嵌h5页面，结合网易云的nos直传   
后台管理页面的扫码登陆：利用websocket的长连接技术----首先登陆页面会接受到一个id（时效性型芯，
此时已经建立长连接），小程序扫码获取id，并且将用户信息发送到服务器，  服务验证后推送信息到客户端，客户端响应，登陆成功，进入首页    
文件下载路径的提示： 调用downloadFile接口，路径为wxfile://tmp(此时文件是放在内存中的)
然后调用openDocument接口，将word\excel等文件直接打开预览，但是图片除了调用saveImageToPhotosAlbum接口之外
，还可以调用saveFile接口，将文件存储在本地wxfile://store(注意此时文件保存到了t/ecent/MicroMsg/wxafiles/目录下)