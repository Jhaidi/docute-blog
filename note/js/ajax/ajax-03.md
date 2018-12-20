# Jquery AJAX

> Ajax 全称为：“Asynchronous异步 JavaScript and XML”（异步 JavaScript 和 XML）， 它并不是 JavaScript 的一种单一技术，而是利用了一系列交互式网页应用相关的技术所形 成的结合体。使用 Ajax，我们可以无刷新状态更新页面，并且实现异步提交，提升了用户体验。

## ajax()

### 定义和方法

> ajax() 方法通过 HTTP 请求加载远程数据。
该方法是 jQuery 底层 AJAX 实现。简单易用的高层实现见 `$.get`, `$.post` 等。`$.ajax()` 返回其创建的 `XMLHttpRequest` 对象。大多数情况下你无需直接操作该函数，除非你需要操作不常用的选项，以获得更多的灵活性。
最简单的情况下，`$.ajax()` 可以不带任何参数直接使用。
> <span style="color: red">注意： 所有的选项都可以通过 `$.ajaxSetup()` 函数来全局设置。</span>

```js
jQuery.ajax([settings])
```

### 参数解析

参数 | 描述
--- | ---
settings | 可选。用于配置 Ajax 请求的键值对集合。可以通过 $.ajaxSetup() 设置任何选项的默认值。

参数 | 类型 | 默认值 | 描述
--- | --- | --- | ---
options | Object | - | 可选。ajax请求设置，所有选项都是可选的。
async | Boolean | true | 默认设置下，所有请求均为异步请求。如果需要发送同步请求，请将此选项设置为 false。注意，同步请求将锁住浏览器，用户其它操作必须等待请求完成才可以执行。
beforeSend(XHR) | Function | 发送请求前可修改 XMLHttpRequest 对象的函数，如添加自定义 HTTP 头。XMLHttpRequest 对象是唯一的参数。这是一个 Ajax 事件。如果返回 false 可以取消本次 ajax 请求。
cache | Boolean | true | dataType 为 script 和 jsonp 时默认为 false。设置为 false 将不缓存此页面。jq版本1.2+
complete(XHR, TS) | Function | - | 请求完成后回调函数 (请求成功或失败之后均调用)。参数： XMLHttpRequest 对象和一个描述请求类型的字符串。这是一个 Ajax 事件。
contentType | String | application/x-www-form-urlencoded (发送信息到服务器是内容编码类型) | 默认值适合大多数情况。如果你明确地传递了一个 content-type 给 $.ajax() 那么它必定会发送给服务器（即使没有数据要发送）。
context | Object | - | 这个对象用于设置 Ajax 相关回调函数的上下文(可理解为this)
data | String | - | 发送到服务器的数据。将自动转换为请求字符串格式。GET 请求中将附加在 URL 后。查看 processData 选项说明以禁止此自动转换。必须为 Key/Value 格式。如果为数组，jQuery 将自动为不同值对应同一个名称。如 {foo:["bar1", "bar2"]} 转换为 '&foo=bar1&foo=bar2'。
dataFilter | Function | - | 给 Ajax 返回的原始数据的进行预处理的函数。提供 data 和 type 两个参数：data 是 Ajax 返回的原始数据，type 是调用 jQuery.ajax 时提供的 dataType 参数。函数返回的值将由 jQuery 进一步处理。
dataType | String | 预期服务器返回的数据类型。1."xml": 返回 XML 文档，可用 jQuery 处理。2."html": 返回纯文本 HTML 信息；包含的 script 标签会在插入 dom 时执行。3."script": 返回纯文本 JavaScript 代码。不会自动缓存结果。除非设置了 "cache" 参数。注意：在远程请求时(不在同一个域下)，所有 POST 请求都将转为 GET 请求。（因为将使用 DOM 的 script标签来加载）4."json": 返回 JSON 数据 。5."jsonp": JSONP 格式。使用 JSONP 形式调用函数时，如"myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。6."text": 返回纯文本字符串 | 如果不指定，jQuery 将自动根据 HTTP 包 MIME 信息来智能判断，比如 XML MIME 类型就被识别为 XML。在 1.4 中，JSON 就会生成一个 JavaScript 对象，而 script 则会执行这个脚本。随后服务器端返回的数据会根据这个值解析后，传递给回调函数。
error | Function | 自动判断（xml或者html)请求失败调用此函数。含有三个参数：XMLHttpRequest 对象、错误信息、（可选）捕获的异常对象。 | 如果发生了错误，错误信息（第二个参数）除了得到 null 之外，还可能是 "timeout", "error", "notmodified" 和 "parsererror"。这是一个 Ajax 事件。
global | Boolean | true | 是否触发全局 AJAX 事件。默认值: true。设置为 false 将不会触发全局 AJAX 事件，如 ajaxStart 或 ajaxStop 可用于控制不同的 Ajax 事件。
ifModified | Boolean | false | 仅在服务器数据改变时获取新数据。使用 HTTP 包 Last-Modified 头信息判断。在 jQuery 1.4 中，它也会检查服务器指定的 'etag' 来确定数据没有被修改过。
jsonp | String | - | 在一个 jsonp 请求中重写回调函数的名字。这个值用来替代在 "callback=?" 这种 GET 或 POST 请求中 URL 参数里的 "callback" 部分，比如 {jsonp:'onJsonPLoad'} 会导致将 "onJsonPLoad=?" 传给服务器。
jsonpCallback | String | - | 为 jsonp 请求指定一个回调函数名。这个值将用来取代 jQuery 自动生成的随机函数名。这主要用来让 jQuery 生成度独特的函数名，这样管理请求更容易，也能方便地提供回调函数和错误处理。你也可以在想让浏览器缓存 GET 请求的时候，指定这个回调函数名。
password | String | - | 用于响应 HTTP 访问认证请求的密码
processData | Boolean | true | 默认情况下，通过data选项传递进来的数据，如果是一个对象(技术上讲只要不是字符串)，都会处理转化成一个查询字符串，以配合默认内容类型 "application/x-www-form-urlencoded"。如果要发送 DOM 树信息或其它不希望转换的信息，请设置为 false。
scriptCharset | String | - | 只有当请求时 dataType 为 "jsonp" 或 "script"，并且 type 是 "GET" 才会用于强制修改 charset。通常只在本地和远程的内容编码不同时使用。
success | Function | - | 请求成功后的回调函数。参数：由服务器返回，并根据 dataType 参数进行处理后的数据；描述状态的字符串。这是一个 Ajax 事件。
traditional | Boolean | -| 如果你想要用传统的方式来序列化数据，那么就设置为 true。请参考工具分类下面的 jQuery.param 方法。
timeout | Number | - |设置请求超时时间（毫秒）。此设置将覆盖全局设置。
type | String | GET | 请求方式 ("POST" 或 "GET")，注意：其它 HTTP 请求方法，如 PUT 和 DELETE 也可以使用，但仅部分浏览器支持。
url | String | 当前页地址 |发送请求的地址。
username | String | - |用于响应 HTTP 访问认证请求的用户名。
xhr | Function | - |需要返回一个 XMLHttpRequest 对象。默认在 IE 下是 ActiveXObject 而其他情况下是 XMLHttpRequest 。用于重写或者提供一个增强的 XMLHttpRequest 对象。这个参数在 jQuery 1.3 以前不可用。

### 回调函数

> 如果要处理 $.ajax() 得到的数据，则需要使用回调函数：beforeSend、error、dataFilter、success、complete。

* beforeSend

在发送请求之前调用，并且传入一个 XMLHttpRequest 作为参数。

* error

在请求出错时调用。传入 XMLHttpRequest 对象，描述错误类型的字符串以及一个异常对象（如果有的话）

* dataFilter

在请求成功之后调用。传入返回的数据以及 "dataType" 参数的值。并且必须返回新的数据（可能是处理过的）传递给 success 回调函数。

* success

当请求之后调用。传入返回后的数据，以及包含成功代码的字符串。

* complete

当请求完成之后调用这个函数，无论成功或失败。传入 XMLHttpRequest 对象，以及一个包含成功或错误代码的字符串。

### 数据类型

$.ajax() 函数依赖服务器提供的信息来处理返回的数据。如果服务器报告说返回的数据是 XML，那么返回的结果就可以用普通的 XML 方法或者 jQuery 的选择器来遍历。如果见得到其他类型，比如 HTML，则数据就以文本形式来对待。
通过 dataType 选项还可以指定其他不同数据处理方式。除了单纯的 XML，还可以指定 html、json、jsonp、script 或者 text。
其中，text 和 xml 类型返回的数据不会经过处理。数据仅仅简单的将 XMLHttpRequest 的 responseText 或 responseHTML 属性传递给 success 回调函数。

### 发送数据到服务器

默认情况下，Ajax 请求使用 GET 方法。如果要使用 POST 方法，可以设定 type 参数值。这个选项也会影响 data 选项中的内容如何发送到服务器。
data 选项既可以包含一个查询字符串，比如 key1=value1&key2=value2 ，也可以是一个映射，比如 {key1: 'value1', key2: 'value2'} 。如果使用了后者的形式，则数据再发送器会被转换成查询字符串。这个处理过程也可以通过设置 processData 选项为 false 来回避。如果我们希望发送一个 XML 对象给服务器时，这种处理可能并不合适。并且在这种情况下，我们也应当改变 contentType 选项的值，用其他合适的 MIME 类型来取代默认的 application/x-www-form-urlencoded 。

## get()

#### 定义和方法

> `get()` 方法通过远程 HTTP GET 请求载入信息。这是一个简单的 GET 请求功能以取代复杂`$.ajax`。请求成功时可调用回调函数。如果需要在出错时执行函数，请使用`$.ajax`。

#### 示例

```js
//示例
$(selector).get(url,data,success(response,status,xhr),dataType)
```

#### 参数

参数 | 描述
--- | ---
url | 必需。规定将请求发送的哪个 URL。
data | 可选。规定连同请求发送到服务器的数据。
success | 可选。规定当请求成功时运行的函数。额外的参数：response - 包含来自请求的结果数据；status - 包含请求的状态；xhr - 包含 XMLHttpRequest 对象
dataType | 可选。规定预计的服务器响应的数据类型。默认地，jQuery 将智能判断。可能的类型："xml""html""text""script""json""jsonp"

## post()

#### 定义与用法

> post()方法通过HTTP POST请求从服务器载入数据

#### 语法

```js
jQuery.post(url,data,success(data, textStatus, jqXHR),dataType)
```

#### 参数

参数 | 描述
--- | ---
url | 必需。规定将请求发送的哪个 URL。
data | 可选。映射或字符串值。规定连同请求发送到服务器的数据。
success(data, textStatus, jqXHR) | 可选。请求成功时执行的回调函数。
dataType | 可选。规定预期的服务器响应的数据类型。默认执行智能判断（xml、json、script 或 html）。


## 练习

url | method | 参数
--- | --- | ---
http://123.56.220.217:3031/list | get | type(Number),name(String)
http://123.56.220.217:3031/submit | post | name(String),其他参数自选
http://123.56.220.217:3031/delete/:id | delete | id(55022299 --- 固定参数) 

以上接口请求成功后返回字段中包含

```js
{
  "msg": "success",
  "code": 200,
  //其他
}
```
