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
- 对象格式 {}
- 数组格式 [{},{},{}]
- 注意：json的key是字符串，json的value是Object
- json中必须用双引号
- javascript可以直接取出json中的数据

### Jquery的Ajax
- get方式
  - $.get(url,[data],[callback],[type])
  - url:访问的url地址
  - data:请求参数，可以直接写json
  - callback:匿名函数，function(data) {执行成功后的回调}
  - type:返回的数据类型(xml,html,script,json,text,_default),常用json和text
  - java返回数据`response.getWriter().write()`
  - get方式提交中文使用编码解码的方法(服务器获取中文参数)
    - 在`request.getParameter()`之前，加入`request.setCharacterEncoding("UTF-8")`

**注意**：java中没有json，所以返回时只能返回一个json格式的字符串

- post方式
  - $.post(url,[data],[callback],[type])
  - jquery提交中文，已经解决了乱码问题，不用管
- 后台返回中文乱码问题解决
  - 在后台写`response.setContentType("text/html;charset=UTF-8")`
  - 这样后台就可以往前台发中文了
  - 后台发中文时有两种方式，是有区别的` response.setContentType("text/html; charset=UTF-8");`不仅发送到浏览器的内容是UTF-8编码，而且还通知浏览器使用UTF-8方式进行解析，所以怎样都能显示中文，还有一种方法是`response.setCharacterEncoding("UTF-8"); `仅仅是发送到浏览器的内容是UTF-8编码，至于浏览器是哪种编码方式显示，它不管
- $.ajax方法(重要)
  - $.ajax({option1:value1,option2:value2})
  - 常用option如下：
  - async:默认true,异步请求
  - contentType:默认"application/x-www-form-urlencoded"，发送信息至服务器时内容的编码类型
  - data:发送到服务器的数据
  - dataType:预期服务器返回的数据类型
  - success:请求成功后的回调函数 function() {}
  - error:请求失败时的回调
  - type:请求方式("GET"或"POST")，默认"GET"
  - url:发送请求的地址
```javascript
    $.ajax({
        async:true,
        url:"WEB/ajaxServlet",
        type:"POST",
        data:{"name":"tom","age":18},
        success: function (data) {
            //data是返回的数据
            console.log(data);
        },
        dataType:"json"
    });
```
### 异步校验用户名是否存在
- 注册时，当失去焦点时(onblur)，异步验证该用户名是否存在
- jquery获得输入框内容: val()
- 访问url的根路径可以动态获取:${pageContext.request.contextPath}

### 站内搜索功能(搜索框中根据输入的部分智能提示结果)
- 监听onkeyup事件，获取输入框内容
- 根据内容异步查询数据库，模糊查询(pname like ?) %keyword%
- 将返回商品名称显示在输入框下方
- 返回的商品为对象或者集合，使用工具转换成json格式的字符串
  - jsonlib
  - Gson
  - fastjson