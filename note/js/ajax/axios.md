## axios使用介绍

### [Api](https://github.com/axios/axios#axiosrequestconfig)

### 在项目中如何使用axios

基础配置：
```js
const ajaxconfig = {
  // 基础url前缀
  baseURL: ajaxBaseURL,
  // 设置超时时间
  timeout: 300000,
  // 返回数据类型
  responseType: 'json', // default
  // 请求的接口，在请求的时候，如axios.get(url,config);这里的url会覆盖掉config中的url
  url: '/',

  // 请求方法同上
  method: 'get', // default
  transformRequest: [function (data) {
    // 这里可以在发送请求之前对请求数据做处理，比如form-data格式化等，这里可以使用开头引入的Qs（这个模块在安装axios的时候就已经安装了，不需要另外安装）
    // data = axios.stringify(data);
    // console.log('data',typeof data,data)
    // console.log(data.method)
    return data
  }],

  transformResponse: [function (data) {
    // 这里提前处理返回的数据
    if (typeof data === 'string') {
      data = JSON.parse(data)
    }
    return data
  }],

  // 请求头信息
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded', // application/json application/x-www-form-urlencoded
  },

  // parameter参数
  params: {},

  // post参数，使用axios.post(url,{},config);如果没有额外的也必须要用一个空对象，否则会报错
  data: {},

  withCredentials: true
  // 当我们把此配置项设置成默认配置项并且设置成true的时候，axios就可以设置cookies了。
}

```

实例axios

```js
let instance = axios.create(ajaxconfig);
// 使用拦截器
instance.interceptors.request.use(function (config) {
  // dosomething
  return config;
}, function (error) {
  // dosomething
  return Promise.reject(error);
});

```

通过上面的配置 我们已经将axios配置完成了，我们可以根据我们不同项目进行部分调整。