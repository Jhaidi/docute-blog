# I Promise has new feelings every time

## 前言

在JavaScript语言中，代码都是是单线程执行的，正是由于这个原因，导致了JavaScript中所有的网络操作，浏览器事件，都必须满足异步执行的要求。所以异步的各种方案开始出现并逐步合理化，简单话！

### 异步处理

在开发过程中大家使用的异步处理方案一般包括：`回调函数（Callback）`、`Promise`、`Generator`函数、`async/await`。这里就主要说一下这些方案的异同：

###### 回调函数（Callback）

假设我们定义一个`getData`函数用于数据请求：

```js
function getData(url, callback) {
  // 模拟数据请求
  setTimeout(() => {
    let res = {
      url: url,
      data: {}
    }
    callback(res)
  }, 1000)
}
```

现在的需求是我们需要依次请求三次服务器，并且每次请求的数据必须在上次成功的基础上执行：

```js
getData('/api/page/1?params=123',(res1) => {
  console.log(res1);
  getData(`/api/page/2?params=${res1.data.params}`, (res2) => {
    console.log(res2);
    getData(`/api/page/3?params=${res2.data.params}`, (res3) => {
      console.log(res3);
    })
  })
})
```

通过上面的🌰，我们可以看到第一次的url:`/api/page/1?params=123`，第二次的url: `/api/page/2?params=${res1.data.params}`，依赖第一次请求的数据，第三次的url：`/api/page/2?params=${res2.data.params}`，依赖第二次请求的数据。由于我们每次的数据请求都依赖上次的请求，所以我们将会将下一次的数据请求以回调函数的形式写在函数内部，这其实就是我们常说的`回掉地狱`！

###### Promise

同样的需求，我们使用`Promise`，去实现看看：

首先我们需要先将我们的`getData`函数改写成`Promise`的形式

```js
  function getDataPromise(url) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        let res = {
          url: url,
          data: {}
        }
        resolve(res)
      }, 1000)
    })
  }
```

那么逻辑代码应该变成：

```js
  getDataPromise('/api/page/1?params=123')
    .then(res1 => {
      console.log(res1);
      return getDataPromise(`/api/page/2?params=${res1.data.params}`)
    })
    .then(res2 => {
      console.log(res2);
      return getDataPromise(`/api/page/3?params=${res2.data.params}`)
    })
    .then(res3 => {
      console.log(res3);
    })
```

这样写完来看，发现我们每次在数据请求成功（`then`）之后返回一个`Promise`对象，方便下次使用，这样我们就避免了`回掉地狱`的出现，但是这样其实也不算事完美，当我们的请求变得复杂的时候我们会发现我们的代码会变的更加复杂。

为了避免这种情况的出现 `async/await`应运而生。

###### async/await

`getData`函数不变，还是`Promise`

```js
  function getDataPromise(url) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        let res = {
          url: url,
          data: {}
        }
        resolve(res)
      }, 1000)
    })
  }
```

需求代码变成：

```js
  async function getData () {
    let res1 = await getDataPromise('/api/page/1?params=123');
    console.log(res1);
    let res2 = await getDataPromise(`/api/page/2?params=${res1.data.params}`);
    console.log(res2);
    let res3 = await getDataPromise(`/api/page/2?params=${res2.data.params}`);
    console.log(res3);
  }

```
怎么样，是不是这段代码阅读起来非常舒服，其实`async/await`都是基于`Promise`的，使用`async`方法最后返回的还是一个`Promise`；实际上`async/await`可以看作是`Generator`异步处理的语法糖，👇我们就来看一下使用`Generator`怎么实现这段代码

####### Generator

```js
  // 异步函数依旧是Promise
  function getDataPromise(url) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        let res = {
          url: url,
          data: {}
        }
        resolve(res)
      }, 1000)
    })
  }

  function * getData() {
    let res1 = yield getDataPromise('/api/page/1?params=123');
    console.log(res1);
    let res2 = yield getDataPromise(`/api/page/2?params=${res1.data.params}`);
    console.log(res2);
    let res3 = yield getDataPromise(`/api/page/2?params=${res2.data.params}`);
    console.log(res3);
  }

```

其实可以分开来看：

```js
  let fn = getData()
  fn.next().value.then(res1 => {
    fn.next(res1).value.then(res2 => {
      fn.next(res2).value.then( () => {
        fn.next()
      })
    })
  })

```

上面的代码我们可以看到，`next()`每一步之行`.value`方法返回的都是一个`Promise`，所以我们可以在后面添加`then`方法，在`then`方法后面我继续调用`next()`，知道函数运行完成。实际上上面的代码我们不需要手动去写，我们可以对其封装一下：

```js
  function run(gen) {
    let fn = gen()

    function next(data) {
      let res = fn.next(data)
      if (res.done) return res.value
      res.value.then((info) => {
        next(info)
      })
    }
    next()
  }

  run(getData)
```
`run`方法用来自动执行一步操作，其实就可以看作是`Generator`在进行`递归`操作;

这样我们就将异步操作封装到了函数内部，其实不难发现`async/await`和`Generator`有很多相似的地方，只不过`async/await`在语义上更容易被理解。

在使用`async/await`的时候我们不需要在去定义`run()`，它内部已经给我们定义封装好了，这也是为什么说`async/await`是`Generator`异步处理的语法糖了。

### Promise

上面我们介绍了`回调函数（Callback）`、`Promise`、`Generator`函数、`async/await`的区别，下面我们就来具体说说`Promise`。

###### Promise.prototype.then()

* 作用

`then` 和 `Promise.prototype.catch()` 方法都会返回 `promise`，它们可以被链式调用 — 一种称为复合`composition` 的操作.

* 参数

第一个参数：状态从 `pending` -> `fulfilled` 时的回调函数

第二个参数：状态从 `pending` -> `rejected` 时的回调函数

* 返回值：新的 `Promise` 实例（**注意不是原来的 `Promise` 实例**）

* 特点

由于 `then` 方法返回一个新的 `Promise` 实例，所以 `then` 方法是可以链式调用的，链式调用的 `then` 方法有两个特点：

第一：后一个 `then` 方法的回调函数的参数是前一个 `then` 方法的返回值

第二：如果前一个 `then` 方法的返回值是一个 `Promise` 实例，那么后一个 `then` 方法的回调函数会等待该 `Promise` 实例的状态改变后再执行

###### Promise.prototype.catch

* 描述

catch 方法可以用于您的promise组合中的错误处理。

Internally calls Promise.prototype.then on the object upon which is called, passing the parameters undefined and the onRejected handler received; then returns the value of that call (which is a Promise).

大家可以看一下下面的代码：

```js
const promise = new Promise(function (resolve, reject) {
    setTimeout(() => {
        reject('err')
    }, 1000)
})

promise.then(
    res => console.log('s1'),
    err => console.log('e1')
).then(
    res => console.log('s2')
).catch(
    err => console.log('e2')
)
```

```log
e1
s2
```

可以发现，在第一个 `then` 方法执行的错误处理函数中捕获到了错误，所以输出了 `e1`，那么这个错误已经被捕获到了，也就不需要 `catch` 再次捕获了，所以没有输出 `e2`，这是正常的，但问题是竟然输出了 `s2`。。。。
所以为了避免这种情况代码应该改为：

```js
promise.then(
    res => console.log('s1')
).then(
    res => console.log('s2')
).catch(
    err => console.log('e2')
)
```

这样只会输出`e2`了

####### Promise.prototype.finally

当我们想在`Promise`无论成功还是失败的时候都想进行某一步操作时，可以说使用`finally`

```js
promise.then(
    res => console.log('s1')
).catch(
    err => console.log('e1')
).finally(
    () => console.log('end')
)
```

很容易能够发现，`.finally` 只不过是一个成功与失败的回调函数相同的 `.then` 而已。

###### Promise.all(iterable);

* 参数(iterable)
一个[可迭代](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol)的对象，如 Array 或 String;

* 返回值
  * 如果传入的参数是一个空的可迭代对象，则返回一个已完成（already resolved）状态的 Promise。
  * 如果传入的参数不包含任何 promise，则返回一个异步完成（asynchronously resolved） Promise。注意：Google Chrome 58 在这种情况下返回一个已完成（already resolved）状态的 Promise。
  * 其它情况下返回一个处理中（pending）的Promise。这个返回的 promise 之后会在所有的 promise 都完成或有一个 promise 失败时异步地变为完成或失败。 见下方关于“Promise.all 的异步或同步”示例。返回值将会按照参数内的 promise 顺序排列，而不是由调用 promise 的完成顺序决定。

🌰：

```js
  const p = Promise.all([promise1, promise2, promise3])

  p.then(
      (res) => {
          // res 是结果数组
      }
  )
```

> 只有当所有 `Promise` 实例的状态都变为 `fulfilled`，那么 `Promise.all` 生成的实例才会 `fulfilled`。
> 只要有一个 `Promise` 实例的状态变成 `rejected`，那么 `Promise.all` 生成的实例就会 `rejected`。

###### Promise.race

* 作用：与 `Promise.all` 类似，也是将多个 `Promise` 实例包装成一个 `Promise` 实例。

* 参数：与 `Promise.all` 相同

* 特点：

`Promise.race` 方法生成的 `Promise` 实例的状态取决于其所包装的所有 `Promise` 实例中状态最先改变的那个 `Promise` 实例的状态。

> race 函数返回一个 Promise，它将与第一个传递的 promise 相同的完成方式被完成。它可以是完成（ resolves），也可以是失败（rejects），这要取决于第一个完成的方式是两个中的哪个。
> 如果传的迭代是空的，则返回的 promise 将永远等待。
> 如果迭代包含一个或多个非承诺值和/或已解决/拒绝的承诺，则 Promise.race 将解析为迭代中找到的第一个值。

* 例子：请求超时

```js
const promise = Promise.race([
    getData('/path/data'),
    new Promise((resolve, reject) => {
        setTimeout(() => { reject('timeout') }, 10000)
    })
])

promise.then(res => console.log(res))
promise.catch(msg => console.log(msg))
```

###### Promise.resolve()

* 作用：将现有对象(或者原始值)转为 `Promise` 对象。

* 参数：参数可以是任意类型，不同的参数其行为不同
  * 如果参数是一个 `Promise` 对象，则原封不动返回
  * 如果参数是一个 `thenable` 对象(即带有 `then` 方法的对象)，则 `Promise.resolve` 会将其转为 `Promise` 对象并立即执行 `then` 方法
  * 如果参数是一个普通对象或原始值，则 `Promise.resolve` 会将其包装成 `Promise` 对象，状态为 `fulfilled`
  * 不带参数，则直接返回一个状态为 `fulfilled` 的 `Promise` 对象

###### Promise.reject()

* 概述

`Promise.reject(reason)`方法返回一个带有拒绝原因reason参数的Promise对象。

> 一般通过使用Error的实例获取错误原因reason对调试和选择性错误捕捉很有帮助。

* 参数：任意参数，该参数将作为失败的理由：

```js
Promise.reject('err')

// 等价于
new Promise(function (resolve, reject) {
    reject('err')
})
```

###### 统一使用Promise

其实我们在js中可以将同步代码也可使用`Promise`

```js

function a() {
  console.log('aaa')
}

// 等价于
const p = new Promise((resolve, rejext) => {
  resolve(a())
})
```

### That's All

或者点击[Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

