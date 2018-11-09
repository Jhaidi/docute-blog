# webpack打包优化

先给大家看一下项目的目录结构：

![s](https://user-gold-cdn.xitu.io/2018/2/17/161a1b40f1e45d43?w=666&h=360&f=png&s=37632)

就是正常的项目结构，简单说一下吧：

- build文件夹包含的是一些打包配置的一下东西
- config文件夹是项目的基础配置
- dist是打包之后的文件
- node_modules是项目的依赖包
- src文件夹里面是项目的源码
- static文件夹里面放的是一些项目使用的静态资源
- index.html是项目的首页
- package.json文件是项目的配置json
- yarn.lock是使用[yarn](https://yarnpkg.com/en/)锁定项目用的依赖

## 优化思路

项目打包时间长，原因无外乎就是项目整体比较庞大、依赖复杂、组件之前拆分不够合理。

对于这三个问题呢，我们可以针对下面这几个方面去做一下处理：

- 对项目进行路由屏蔽，只打包自己需要打包的部分（我司就是好几个项目合并在了一起，至于原因则是 需求类似，所以放在一起比较省事 -_-||| 开发过程中是省了不少事，但是现在一样要还的！！！！）
- 依赖关系复杂，这里说的是项目中的依赖模块比较多，像我们现在这个项目，光算依赖包的话就有40+，另外一个重要原因就是组件之间存在相同引用的依赖。解决思路是把项目中重用的依赖抽离出来进行单独打包。
- 组件在写的过程中，需要考虑好这个组件的使用方向，以及实现功能，不能混为一谈。

## 实际操作

有了整体的思路，那么开搞就可以啦
去[webpack](https://doc.webpack-china.org/)文档去看了一下有一个[DllPlugin](https://doc.webpack-china.org/plugins/dll-plugin/#dllplugin)，这个插件就是帮助我们解决问题的关键，下面是我webpack.dll.config的代码：

```js
var path = require("path");
var webpack = require("webpack");

function resolve (dir) {
  return path.join(__dirname, '..', dir)
}

module.exports = {
  // 你想要打包的模块的数组
  entry: {
    vendor: ['vue', 'lodash', 'vuex', 'axios', 'vue-router', 'iview', 'element-ui',
      'echarts','xlsx','jquery','vue-fullcalendar','vue-cookie','handsontable']
  },
  output: {
    path: path.join(__dirname, '../dist/vendor-dll-js'), // 打包后文件输出的位置
    filename: '[name].dll.js',
    library: '[name]_library'
    // vendor.dll.js中暴露出的全局变量名。
    // 主要是给DllPlugin中的name使用，
    // 故这里需要和webpack.DllPlugin中的`name: '[name]_library',`保持一致。
  },
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src'),
    }
  },
  plugins: [
    new webpack.DllPlugin({
      path: path.join(__dirname, '.', '[name]-manifest.json'),
      name: '[name]_library',
      context: __dirname
    }),
    // 压缩打包的文件，与该文章主线无关
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
};
```

我们需要将项目中具有重用性的包抽离出来，放在vendor数组里面，然后在下面output里面定义一下打包输出的文件路径，然后在resolve里面配置解析参数，最后定义使用的DllPlugin插件，UglifyJsPlugin是压缩js的插件

Dllplugin里的path，会输出一个vendor-manifest.json，这是用来做关联id的，打包的时候不会打包进去，所以不用放到static里
然后运行一下
`webpack -p --progress --config build/webpack.dll.conf.js`

成功以后，static下会有dll.vendor.js，根目录下会有vendor.manifest.json
各自打开看一下，就会看到依赖库的源码和匹配id

ok，到这里，抽离依赖库的事情就完成了，那么接下来问题就是怎么引用呢，怎么在dev和build跑呢？

这里补了一点dll和commonsChunk概念上的区别，commonsChunk之所以慢和大，是因为每次run的时候，都会去做一次打包，而实际上我们不会一直去更新我们引用的依赖库，所以dll的做法就等于是，事先先打包好依赖库，然后只对每次都修改的js做打包。

继续上面的步骤，我们需要根据生成的json文件去修改webpack.base.config文件：

```js
const manifest = require('../vendor-manifest.json')
......
plugins: [
    new webpack.DllReferencePlugin({
      manifest
    })
  ]
```

然后打开index.html，在底部加上`<script src="./static/dll.vendor.js"></script>`；

执行一下`npm run build`,一起正常的话，表示你的操作是正确的。

## 升级处理

至此优化的问题基本已经解决了，但是在处理过程中需要进行复制粘贴，还要对index.html文件进行操作，如果是对于项目不熟悉的人来进行开发项目的话，就会出现一些小的问题，所以我决定继续往下研究一下：

思路还是上面的思路，我们下面需要进行的操作呢就是对与之前的处理进行优化，通过配置文件，和命令去实现我们想要的效果

首先我们将上面 webpack.dll.config 文件里面的entry配置项拿出来，在config文件夹下新建一个dll.js

```js
module.exports = {
  entry: {
    // 这里的依赖顺序必须是：对象从上往下依赖，数组从右到左依赖（如果互不依赖的可以忽略顺序）
    ui: ['iview', 'element-ui'],
    tool: ['lodash', 'jquery', 'axios', 'vue-fullcalendar'],
    vue: ['vue', 'vuex', 'vue-router', 'vue-cookie'],
    xlsx: ['xlsx'],
    echarts: ['echarts'],
    other: ['handsontable'],
  },
  outFile: '../static/dll'
};
```

这里面其实就是我们一开始写的entry的配置项，根据这个js去打包的文件有一个顺序，就是我总结的这个：

- 这里的依赖顺序必须是：对象从上往下依赖，数组从右到左依赖（如果互不依赖的可以忽略顺序）

如果不按照这个顺序去写的话，会出现依赖错误的问题！！！

然后在output里面再进行一下配置：

```js
output: {
    path: path.join(__dirname, dllConfig.outFile), // 打包后文件输出的位置
    filename: '[name].dll.[chunkhash].js',
    library: '[name]_library'
    // 主要是给DllPlugin中的name使用，
    // 故这里需要和webpack.DllPlugin中的`name: '[name]_library',`保持一致。
  },
```

这样在执行`webpack -p --progress --config build/webpack.dll.conf.js`指令的时候会生成如下：

![a](https://user-gold-cdn.xitu.io/2018/2/17/161a1f5df066b652?w=726&h=980&f=png&s=147654)
是不是看到文件后面的hash就一脸懵逼，这怎么办，我们没有办法去进行复制粘贴了！！（我们的目的不就是不进行复制粘贴吗  正经脸-_-）

要实现命令操作之后不进行复制粘贴操作就需要使用webpack的 [HtmlWebpackPlugin](https://doc.webpack-china.org/plugins/html-webpack-plugin/#src/components/Sidebar/Sidebar.jsx)插件

在plugins里面配置一下HtmlWebpackPlugin

```js
new HtmlWebpackPlugin({
  filename: path.join(__dirname, '../', config.dev.index),
  template: 'index.ejs',
  inject: false
}),
```

然后在根目录添加一个index.ejs模版（[ejsGitHub地址](https://github.com/tj/ejs)），
index.ejs中代码如下:

```html
<body>
    <div id="app"></div>
    <!-- dll files will be auto injected -->
    <% for (var chunk in htmlWebpackPlugin.files.chunks) { %><script type="text/javascript" src="/<%= htmlWebpackPlugin.files.chunks[chunk].entry %>"></script>
    <% } %>
    <!-- built files will be auto injected -->
</body>
```

最后需要在config文件夹下的index.js进行一下修改：
在dev中添加：`index: 'index.html',`

项目在执行dev指令或者build指令之前需要先执行：`webpack -p --progress --config build/webpack.dll.conf.js`
在dll指令结束后 执行其他操作就可以完美的玩耍了😊

## 总结

至此代码打包优化的整个过程就基本结束了，测试一下，15分钟左右就可以完成打包，比之前打包快了将近10分钟，可以说是非常成功的一次尝试！！