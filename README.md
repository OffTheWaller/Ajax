## Ajax学习+练习
### 原生（开发中基本不用）
- 异步访问、局部刷新
```javascript
//1.创建ajax引擎对象
var xmlHttp = new XMLHttpRequest();
//2.绑定监听---监听服务器是否已经返回数据
xmlHttp.onreadystatechange = function() {
    //5.接受相应数据
    if (xmlHttp.readyState == 4 && xml.Http.status == 200) {
        var res = xmlHttp.responseText;
    } 
}
//3.绑定地址
xmlHttp.open('GET','/WEB/ajaxServlet',true)  //true代表异步方式提价，false代表同步方式提交
//4.发送请求
xmlHttp.send()

```
- 注意：事件绑定的方法不能写在window.onload中，不然属于方法内部的方法，访问不到
- 如果是以get方式提交直接在?后面写数据就可以。例如：?name=zs
- 如果是post方式提交，将提交的数据放在send()里
    - xmlHttp.send('name=zs')
    - 要在send()之前加上`xmlHttp.setRequestHeader("Content-type","application/x-www-form-urlencoded")`

### json数据格式