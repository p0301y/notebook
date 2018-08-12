## ie css hack
- 在属性前面加上下划线"_",那么此属性只会被ie6解释
- 在属性前加星号"*",只会被ie7解释
- 在属性值后加上"\9",表示属性只会被ie8解释

## 修改浏览器中的默认滚动条样式
```
/*滚动条 start*/
::-webkit-scrollbar {
  width: 1px;
  height: 4px;
  background-color: #F5F5F5;
}
/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
  background: #fff ;
}
/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 3px;
  box-shadow: inset 0 0 6px rgba(0,0,0,.3);
  // background-color:rgba(7, 170, 247, 0.7);
  background-color: transparent;
}
::-webkit-scrollbar-thumb:hover {
  border-radius: 3px;
  box-shadow: inset 0 0 6px rgba(0,0,0,.3);
  background-color:rgba(7, 170, 247, 1);
}
```
