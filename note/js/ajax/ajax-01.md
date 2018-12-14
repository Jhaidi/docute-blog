## 服务器

什么是服务器：咱们的页面来源于服务器；实例（在phpnwo上面存放一个页面），
咱们把页面放在互联网的服务器上，就有了自己的网站了。

## 异步同步

生活中的同步：  
![生活中的同步](../../../asset/img/ajax1-0.gif)


生活中的异步：
![生活中的异步](../../../asset/img/ajax1-1.gif)


在JavaScript语言中，同步和异步的概念刚好相反。

这JavaScript中同步就是：你不执行完上面的代码，那么下面的代码你就别执行；一步一步执行，这就是同步。

异步就是可以一块执行的代码；

进程的概念

    进程≠程序

    程序从开始到结束的一次执行过程叫做进程

    一个进程当中，程序同时运行的多个分支，叫做线程

    多线程异步执行，可以提高程序的效率


AJAX的重要性

在许多数企业看来AJAX的使用熟练程度 === 你的工作经验。

## 什么是AJAX

ajax是前后端数据交互的重要手段

Ajax 全称为：“Asynchronous JavaScript and XML”（异步 JavaScript 和 XML）， 它并不是 JavaScript 的一种单一技术，而是利用了一系列交互式网页应用相关的技术所形 成的结合体。使用 Ajax，我们可以无刷新状态更新页面，并且实现异步提交，提升了用户体验。

一．Ajax 概述


Ajax 这个概念是由 JesseJamesGarrett 在 2005 年发明的。它本身不是单一技术，是一串 技术的集合，主要有：

* JavaScript，通过用户或其他与浏览器相关事件捕获交互行为

* XMLHttpRequest 对象，通过这个对象可以在不中断其它浏览器任务的情况下向服务 器发送请求；

* 服务器上的文件，以 XML、HTML 或 JSON 格式保存文本数据；

* 其它 JavaScript，解释来自服务器的数据（比如 PHP 从 MySQL 获取的数据）并将其 呈现到页面上。

由于 Ajax 包含众多特性，优势与不足也非常明显。<span style="color: red">优势</span>主要以下几点：

* 不需要插件支持（一般浏览器且默认开启 JavaScript 即可）；

* 用户体验极佳（不刷新页面即可获取可更新的数据）；

* 提升 Web 程序的性能（在传递数据方面做到按需放松，不必整体提交）；

* 减轻服务器和带宽的负担（将服务器的一些操作转移到客户端）；

而 Ajax 的<span style="color: red">不足</span>由以下几点：
* 不同版本的浏览器度 XMLHttpRequest 对象支持度不足(比如 IE5 之前)；

* 前进、后退的功能被破坏（因为 Ajax 永远在当前页，不会几率前后页面）；
* 搜索引擎的支持度不够（因为搜索引擎爬虫还不能理解 JS 引起变化数据的内容）；
* 开发调试工具缺乏（相对于其他语言的工具集来说，JS 或 Ajax 调试开发少的可怜） 。

## AJAX的使用

电话的接打顺序：
![打电话](../../../asset/img/ajax1-2.png)

1.首先要有一个电话；

2.拨号；

3.说话；

4.听电话另一边的信息；

//有一个电话：创建请求对象；

1.var AJAX=new XMLHttpRequest( );

//拨号：设置请求参数；

2.AJAX.open('get','data/test.json',true);

第一个参数：POST||GET

POST和GET的区别


POST是发送数据，GET是接受数据；

PSOT发送数据的安全性较好，而GET较差；

POST发送数据不限制大小，而GET大小受限2~100k。

什么时候用GET和POST那：在数据获取时用GET方式，在操作数据时应使用POST方式。

第三个参数：当该boolean值为true时，服务器请求是异步进行的，也就是脚本执行send（）方法后不等待

服务器的执行结果，而是继续执行脚本代码；

当该boolean值为false时，服务器请求是同步进行的，也就是脚本执行send（）方法后等待

服务器的执行结果的返回，若在等待过程中超时，则不再等待，继续执行后面的脚本代码！
```js
ajax.onreadystatechange = function()
  {
      if (ajax.readyState == 4 && ajax.status == 200)
      {
          func_succ(ajax.responseText);
      }
      else
      {
          //alert("ajax faild readyState:"+ajax.readyState+" status:"+ajax.status);
      }
  };
```

```js
ajax.send(null);

ajax.readyState

```



0 － （未初始化）还没有调用send()方法

1 － （载入）已调用send()方法，正在发送请求

2 － （载入完成）send()方法执行完成，已经接收到全部响应内容

3 － （交互）正在解析响应内容

4 － （完成）响应内容解析完成，可以在客户端调用了

## AJAX状态码


1**：请求收到，继续处理
2**：操作成功收到，分析、接受
3**：完成此请求必须进一步处理
4**：请求包含一个错误语法或不能完成
5**：服务器执行一个完全有效请求失败

100——客户必须继续发出请求

101——客户要求服务器根据请求转换HTTP协议版本

200——交易成功

201——提示知道新文件的URL

202——接受和处理、但处理未完成

203——返回信息不确定或不完整

204——请求收到，但返回信息为空

205——服务器完成了请求，用户代理必须复位当前已经浏览过的文件

206——服务器已经完成了部分用户的GET请求

300——请求的资源可在多处得到

301——删除请求数据

302——在其他地址发现了请求数据

303——建议客户访问其他URL或访问方式

304——客户端已经执行了GET，但文件未变化

305——请求的资源必须从服务器指定的地址得到

306——前一版本HTTP中使用的代码，现行版本中不再使用

307——申明请求的资源临时性删除

400——错误请求，如语法错误

401——请求授权失败

402——保留有效ChargeTo头响应

403——请求不允许

404——没有发现文件、查询或URl

405——用户在Request-Line字段定义的方法不允许

406——根据用户发送的Accept拖，请求资源不可访问

407——类似401，用户必须首先在代理服务器上得到授权

408——客户端没有在用户指定的饿时间内完成请求

409——对当前资源状态，请求不能完成

410——服务器上不再有此资源且无进一步的参考地址

411——服务器拒绝用户定义的Content-Length属性请求

412——一个或多个请求头字段在当前请求中错误

413——请求的资源大于服务器允许的大小

414——请求的资源URL长于服务器允许的长度

415——请求资源不支持请求项目格式

416——请求中包含Range请求头字段，在当前请求资源范围内没有range指示值，请求也不包含If-Range请求头字段

417——服务器不满足请求Expect头字段指定的期望值，如果是代理服务器，可能是下一级服务器不能满足请求

500——服务器产生内部错误

501——服务器不支持请求的函数

502——服务器暂时不可用，有时是为了防止发生系统过载

503——服务器过载或暂停维修

504——关口过载，服务器使用另一个关口或服务来响应用户，等待时间设定值较长

505——服务器不支持或拒绝支请求头中指定的HTTP版本



学过状态码之后我们可以做一些事情：

```js
var AJAX=new XMHttprequert( );

//拨号：设置请求参数；

AJAX.open('get','data/test.json',true);

ajax.onreadystatechange = function() {
    if (ajax.readyState == 4 && ajax.status == 200)
    {
        func_succ(ajax.responseText);
    }
    else
    {
        //alert("ajax faild readyState:"+ajax.readyState+" status:"+ajax.status);
    }
};

4.ajax.send(null);
```

## Ajax兼容性和简单封装

```js
function InitAjax() {
  var ajax = false;
  try {
    ajax = new ActiveXObject("Msxml2.XMLHTTP");
  } catch (e) {
    try {
      ajax = new ActiveXObject("Microsoft.XMLHTTP");
    } catch (E) {
      ajax = false;
    }
  }

  if (!ajax && typeof XMLHttpRequest != 'undefined') {
    ajax = new XMLHttpRequest();
  }
  return ajax;
}

function DoAjaxGet(ajax, url, func_succ) {
  ajax.open("GET", url, true);
  ajax.onreadystatechange = function () {
    if (ajax.readyState == 4 && ajax.status == 200) {
      func_succ(ajax.responseText);
    } else {
      //alert("ajax faild readyState:"+ajax.readyState+" status:"+ajax.status);
    }
  };
  ajax.send(null);
}

function DoAjaxPost(ajax, url, func_succ, post_datas) {
  ajax.open("POST", url, true);
  ajax.onreadystatechange = function () {
    if (ajax.readyState == 4 && ajax.status == 200) {
      func_succ(ajax.responseText);
    } else {
      alert('ajax faild readyState:' + ajax.readyState + " status:" + ajax.status);
    }
  };
  ajax.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
  ajax.send(post_datas);
}
```

ajax缓存问题：用时间戳清除缓存；

`DoAjaxGet(ajax,'data/test.txt?t='+ new Date().getTime(),aaa)`

测试1.

获取.txt文本文档。

获取JSON

`eval()`方法：将字符串转换成JavaScript可以认识的编码；

拓展：

服务器的交互以及JOSN的格式。

后台语言：PHP，JAVAWEB返回值的不同。

拓展2：

JSON格式：

1.键值对格式：

一般情况用于用户登录接口。

```js
{ "firstName": "Brett", "lastName":"McLaughlin", "email": "aaaa" }
```

2.数组格式：

数组格式1：

```js
{ "people": [
{ "firstName": "Brett", "lastName":"McLaughlin", "email": "aaaa" },
{ "firstName": "Jason", "lastName":"Hunter", "email": "bbbb"},
{ "firstName": "Elliotte", "lastName":"Harold", "email": "cccc" }
]}
```


数组格式2：
```json
{ "programmers": [
{ "firstName": "Brett", "lastName":"McLaughlin", "email": "aaaa" },
{ "firstName": "Jason", "lastName":"Hunter", "email": "bbbb" },
{ "firstName": "Elliotte", "lastName":"Harold", "email": "cccc" }
],
"authors": [
{ "firstName": "Isaac", "lastName": "Asimov", "genre": "science fiction" },
{ "firstName": "Tad", "lastName": "Williams", "genre": "fantasy" },
{ "firstName": "Frank", "lastName": "Peretti", "genre": "christian fiction" }
],
"musicians": [
{ "firstName": "Eric", "lastName": "Clapton", "instrument": "guitar" },
{ "firstName": "Sergei", "lastName": "Rachmaninoff", "instrument": "piano" }
] }
```

json的循环以及使用。
