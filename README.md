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
  - dataType:预期服务器返回的数据类型,jsonp为跨域
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

### 封装一个ajax方法
```js
var $={
    //新增的函数，函数的功能，将一个对象转换成字符串
    params:function(obj){
        //将对象的key 作为参数名，key 对应的值作为参数值。这个地方怎么编写逻辑.
        var str="";
        //username=zhangsan&age=11&sex=nan
        for(var key in obj){
            str+=key+"="+obj[key]+"&";
        }
        //组装的字符串多了一个&,截取之后返回一个新的字符串
        str=str.substr(0,str.length-1);
        return str;//出来是要发送到后台的数据，参数名，参数值.
    },
    ajax:function(obj){
        //1:创建对象
        var xhr=new XMLHttpRequest();
        //新增功能1:我要处理，这个data 对应的值支持是一个对象
        //将 对象={username:"zhangsan",age:11,sex:"nan"}  转换成 字符串的格式  username=zhangsan&age=11&sex=nan

        //新增功能4：请求发送之前调用. 如果该函数return false ，我就组织改代码继续往下执行.
        //console.log(obj.beforeSend);
        if(obj.beforeSend){ //因为用户在调用的时候，可能没有传递,所以需要进行判断.
            var flag=obj.beforeSend();
            if(flag==false){
                return;
            }
        }
        //对obj.data 的类型进行判断，如果是对象类型，我才去进行处理.
        if(typeof obj.data=="object"){
            //在这里进行处理
            //提炼一个方法，参数是obj={} 出来的字符串username=zhangsan&age=11&sex=nan
            //this 现在指向到 $
            obj.data=this.params(obj.data);
        }
        //2:打开对象
        //注意：如果是get 方式提交，参数在地址的后面
        if(obj.type.toLowerCase()=="get"){
            obj.url=obj.url+"?"+obj.data;
            obj.data=null;
        }
        xhr.open(obj.type,obj.url);
        //注意：处理post 我们需要给一个请求头
        if(obj.type.toLowerCase()=="post"){
            xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
        }
        //3:发送数据
        xhr.send(obj.data);
        //4:接收数据
        xhr.onreadystatechange=function(){
            if(xhr.readyState==4){  //响应完成
                if(xhr.status==200){ //响应成功
                    var data=xhr.responseText;
                    obj.success(data);
                }else{
                    //失败的时候去调用error
                    //新增功能2
                    //等价于
                   /* if(obj.error){
                        obj.error();
                    }*/
                    obj.error && obj.error();

                }
                //新增功能3
                //请求完成的时候调用
                // if(obj.complete){
                //     obj.complete();
                // }
                    obj.complete && obj.complete();

            }
        }
    }
}
```

### jsonp跨域
- 原理：利用动态创建script标签，向服务器发送一个请求
- 这个请求并不是xhr的请求，而是一个script请求
- 使用方法：
  - 客户端在请求地址的后面拼接?callback=getInfo
  - 服务端拿到callback函数，并把传递的值当成参数，最后返回给客户端一个getInfo(这里面就是要传递的数据)
  - 客户端在function getInfo () {}里面写处理数据的逻辑代码即可
> jsonp需要客户端和服务端都做处理，并且只支持get方式提交

```js
    document.querySelector('input').onclick = function () {
      //动态创建script标签
      var script = document.createElement('script');
      //把回调函数当做参数传过去，后端需要拿到这个callback，这是后端做的事，把数据包装好
      //返回给客户端的是这个函数执行
      script.src = 'https://sug.so.360.cn/suggest?callback=getInfo&encodein=utf-8&encodeout=utf-8&format=json&fields=word&word=n';
      //把script标签动态插入到body中，就会发一个script请求
      document.body.appendChild(script);
    }
    //这里是处理接收跨域数据后，类似于回调函数
    //后端返回的直接是这个函数的执行，所以会自动调用你自己定义的回调函数，从而处理获取的数据
    function getInfo (data) {
      console.log(data)
    }
```
- jquery给我们封装的$.ajax支持jsonp形式的跨域，只需要把dataType属性设置为jsonp，发送请求即可
```js
    $(function () {
      $('input').on('click', function () {
        $.ajax({
          url: 'https://sug.so.360.cn/suggest',
          data: 'encodein=utf-8&encodeout=utf-8&format=json&fields=word&word=n',
          type: 'get',
          dataType: 'jsonp',
          success: function (data) {
            console.log(data)
          }
        })
      })
    });
```
### CORS跨域

- 通过服务端给客户端输出一个响应头
  header("Access-Control-Allow-Origin:*");  跨域资源共享

- CORS跨域存在安全性问题，解决方案：1.session验证 2.token验证

  ​