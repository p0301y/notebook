# mongodb介绍
- 数据库(dbs) -> 集合(collection) -> 文档(document)
- 创建数据库 use (databaseName)
- 查看数据库 show dbs
- 给指定的数据库插入数据 db.(documentName).insert({})
- 查看所有集合 show collections
- 查询指定集合的数据（文档）
    1. 查询所有 db.(documentName).find()
    2. 查询第一条数据 db.(documentName).findOne()
    3. 指定查询 db.(documentName).find({age},{键指定})
    例如：查询年龄在23~27的名字
    db.(documentName).find({age:{$gte:27,$lte:23}},{name})
- 更新文档数据 db.person.update({oldvalue},{newValue})
- 删除文档数据 db.persons.remove({..})
- 根据条件删除数据 db.person.remove({_id:1})
- 删除文档 db.(documentName).drop()
- 删除数据库 db.dropDatabase()
- db.help() 查看所有db的相关方法
- 批量插入文档（shell支持js）
    ```
    for(var i=0;i<10;i++){
        db.person.insert({name: "people"})
    }
    ```
- 总结的操作步骤
    1. 显示数据库 show dbs
    2. 进入数据库 use db
    3. 显示集合 show collections
    4. 显示文档 db.collection.find()

# mongoose: node.js(express)下用来连接mongodb: npm install mongoose --save
1. express链接mongodb
```
const mongoose = require("mongoose")
mongoose.Promise = global.Promise
mongoose.connect('mongodb://127.0.0.1:27017/test')
```
2. mongoose的使用：Schema => model => documents
    - Schema模式
    ```
    var mongoose = require("mongoose")
    var schema = new mongoose.Schema({
        name: String,
        age: number
    })
    ```
    - model
    ```
    var model = mongoose.model("model",schema)
    ```
    - document
    ```
    var document = new model({name: "hfaj",age:45})
    document.save(function(err){
        if(err) return errorhHandler(err)
    })
    ```
