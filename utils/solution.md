1. 手动实现select，并且可以选择也可以输出
    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
        <style>
            .yh_name{
                width: 170px;
                padding-left: 10px;
                height: 34px;
                outline: none;
            }
            .item:hover{
                cursor: pointer;
            }
        </style>
    </head>
    <body>
    <input type="text" class="yh_name"
           onfocus="two()" onblur="one()">
    <ul id="sel_ul" style="padding-left: 10px;margin:0;width: 172px;border: 1px solid #dddddd;list-style: none;display: none">
        <li class="item">123456</li>
        <li class="item">abcde</li>
        <li class="item">cedsfg</li>
    </ul>
    <script>
        var global = {
            inp: document.getElementsByClassName("yh_name")[0],
            ul:document.getElementById("sel_ul")
        }
        function two() {
            //debugger
            global.ul.style.display = 'block'
        }
        function one() {
            //debugger
            setTimeout(function () {
                global.ul.style.display = "none"
            },300)

        }
        global.ul.addEventListener("click",function (event) {
            var tar = event.target
            if( tar.tagName == "LI"){
                console.log(tar.innerText)
                global.inp.value = tar.innerText
                this.style.display = "none"
            }
        })
    </script>
    </body>
    </html>
    ```
2. 实现excel的下载功能
    - 从后端接受json数据，前端处理生成excel下载；
    JsonExportExcel的github地址：(自己搜索)；
    这种方式比较适用于该数据需要能够导出下载并且同时要展现在页面的场景
    - 通过form表单接受文件（js生成隐藏表单）；如果后端已经生成了excel，就
    不需要在前端处理生成，但是ajax能够返回的数据格式只能为html，script，json，
    xml，不能直接接受excel文件，如果直接通过ajax去获取文件就会报错，但是我们可以
    利用表单可以支持文件传输的特性，通过表单顺利的接受文件
    ```
    <form action="ajax_url">
        <input type="text" name="yourname" value="yourvalue">
        <input type="submit" value="提交">
    </form>
    ```
    - 从后端接受文件地址，设置href下载；
    这种方法同样是通过后端生成excel文件，但并不直接返回excel文件，前端通过向后端发送
    ajax请求返回一个用于下载的地址，在页面上设置a标签的href为返回地址即可