## nodejs的加密技术
- md5加密技术与bcrypt加密技术

    md5加密是采用hash算法，将一个字符串转化成16bytes的字符串存储在数据库中
    例如password对应的是"afkajfklaa678fafa..."，它的速度比较快，换句话说效率比较高
    ，但是安全性差；相比与md5,bcrypt技术就更加安全，在md5的基础上，采用了加盐“salt”算法
    生成一个随机数salt，然后在数据库中记录salt和h=(pwd + salt)，查询的时候得到用户口令p
    ，然后从数据库中查出salt，计算hash(p + salt),看是不是等于h

```javascript
comparePassword: function (_password,cb) {
        var password = bcrypt.hashSync(_password, bcrypt.genSaltSync(SALT_WORK_FACTOR));
        var isMatch = bcrypt.compareSync(_password, password)
        cb(null,isMatch)
}
```

## ssh加密
1. 公钥和私钥
    - 一个公钥对应一个私钥
    - 密钥对中，让大家都知道的是公钥，不要告诉大家，只有自己知道，是私钥
    - 如果用其中一个密钥加密数据，则只有对应的那个密钥才可以解密
    - 如果其中一个密钥可以进行解密数据，则该数据必然是对应的那个密钥进行加密
2. RSA算法的作用
    - 加密： 公钥加密私钥解密  
    主要用于将数据资料加密不被其他人非法获取，保证数据的安全性。使用公钥将数据资料加密，只有私钥可以解密。即加密
    文在网络上被第三方获取，由于没有私钥则无法解密，从而保证数据安全性   
    1. A在自己的电脑上生成SRA钥匙文件，一个私钥文件一个公钥文件，并将他的公钥传给B
    2. 此时B要传送信息给A，于是B用A的公钥加密他的消息，然后传递给A
    3. A用他的私钥解密B的消息
    - 认证： 私钥加密公钥解密   
    主要用于身份验证，判断某个身份的真实性。使用私钥加密之后，用对应的公钥解密从而验证身份的真实性   
    A要验证B是否是真实用户  
    1. B将公钥给A
    2. B将文件用自己私钥加密传给A
    3. A根据B的公钥解密，如果成功则认为真实身份用户
    - 总结  
    1. 私钥用来解密和签名，是给别人用的
    2. 公钥由本人公开，用于加密和验证签名，是给别人用的
    3. 当用户发送文件时，用私钥签名，别人用他给的公钥验证签名，可以保证信息是由他发送的。当用户接受文件时，别人用他的
    公钥加密，他用私钥解密，可以保证信息只能由他接收到
