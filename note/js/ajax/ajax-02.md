## 什么情况下会发生跨域：

![跨域](../../../asset/img/ajax2-1.png)
当本机请求服务器上数据的时候：会发生跨域；

当本地服务器请求其他服务器数据的时候回发生跨域；

跨域怎么处理那？

## 跨域：

#### 为什么要跨域。

同源策略：基于浏览器的安全考虑，浏览器各个厂商之间出现了一个约定，这个约定叫做同源策略。这个约定的主要内容就是，域和域之间数据不共通。

#### 跨域的用途。

![用途](../../../asset/img/ajajx2-2.png)

当网站发展壮大到一定地步的时候，会建立很多的节点，各个节点的IP是不同的，所以跨域问题就会出现。

当测试阶段数据和本机的IP不通用的时候，那么跨域问题也会发生。

#### 为什么要跨域。

因为要获得其他域中的数据；

#### 跨域的原理。

JOSNP

JSONP是什么那？

说到JSONP不得不说一说程序员了：

![猿](../../../asset/img/ajax2-3.gif)


JSONP是伟大程序员与安全策略的斗争中发明的；

JSONP就是在URL中调用function；

```php
<?php
header('Content-type: application/json');
//获取回调函数名
$jsoncallback = htmlspecialchars($_REQUEST ['jsoncallback']);
//json数据
$json_data = '["customername1","customername2"]';
//输出jsonp格式的数据
echo $jsoncallback . "(" . $json_data . ")";
?>
```
```js
<script type="text/javascript">
function callbackFunction(result, methodName) {
    var html = '<ul>';
    for(var i = 0; i < result.length; i++)
    {
        html += '<li>' + result[i] + '</li>';
    }
    html += '</ul>';
    document.getElementById('divCustomers').innerHTML = html;
}
</script>
```

完整代码

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>JSONP 实例</title>
</head>
<body>
    <div id="divCustomers"></div>
    <script type="text/javascript">
function callbackFunction(result, methodName)
        {
            var html = '<ul>';
            for(var i = 0; i < result.length; i++)
            {
                html += '<li>' + result[i] + '</li>';
            }
            html += '</ul>';
            document.getElementById('divCustomers').innerHTML = html;
        }
</script>
<script type="text/javascript" src="http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=callbackFunction"></script>
</body>
</html>
```

jquery 使用JSONP

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>JSONP 实例</title>
    <script src="http://cdn.static.runoob.com/libs/jquery/1.8.3/jquery.js"></script>    
</head>
<body>
<div id="divCustomers"></div>
<script>
$.getJSON("http://www.runoob.com/try/ajax/jsonp.php?jsoncallback=?", function(data) {
    
    var html = '<ul>';
    for(var i = 0; i < data.length; i++)
    {
        html += '<li>' + data[i] + '</li>';
    }
    html += '</ul>';
    
    $('#divCustomers').html(html); 
});
</script>
</body>
</html>
```

#### 跨域应用。

跨域定位：

```js
function handle(res){

  alert(res.city+":"+res.region_name+":"+res.ip)

}

var script=document.createElement('script');

script.src='http://freegeoip.net/json/?callback=handle'
window.onload=function(){

     body.appendchild(script)

}
```