1. 富文本编辑
    - html5的contenteditable属性 + document.execCommand(aCommandName, aShowDefaultUI, aValueArgument)
2. 显示用户通知Notification
```javascript
function notifyMe(){
    //理解js中的in操作符（判断对象中是否包含某个属性）
    if (!('Nitofication' in window)){
        alert("the browser does not support desktop notification")
    } 
    
    else if (Notification.permission === 'granted') {
        var notification = new Notification("hello")
    }
    
    else if (Notification.permission === 'denied') {
        Notification.requestPermission(function(permission) {
          if (permission === 'granted') {
              var notification = new Notification("hello")
          } 
        })
    }
}
```