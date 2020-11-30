# [新手]使用官方的样例来初步运行一个`electron`项目

`electron`官方为我们提供了一个样例来初步认识怎么运行一个`electron`项目；

我们可以按照以下步骤来下载、运行这个样例项目；

```
# 克隆示例项目的仓库
$ git clone https://github.com/electron/electron-quick-start

# 进入这个仓库
$ cd electron-quick-start

# 安装依赖并运行
$ npm install && npm start
```

如果`npm install`失败了，那么需要删除`package-lock.json`重新下载`electron`；

在下载`electron`时，`npm`会下载一个`electron`基础包，然后执行这个包的`postInstall`， 利用它所依赖的`electron-download`来下载平台实际需要的`electron`压缩包，也就是说真正的`electron`并没有保存在`npm`库中，只是放了一个下载器（莫名觉得有些畜生）；
 而这个压缩包会在`s3`上面下载，基本很难下载下来，即使翻墙也很费劲。

下面直接说解决方案，顺便提供了`sass`等库的下载方法；

- 进入`~/.npmrc`

- 增加如下内容；

  ```
  registry=[https://registry.npm.taobao.org](https://link.jianshu.com?t=https%3A%2F%2Fregistry.npm.taobao.org)
   sass_binary_site=[https://npm.taobao.org/mirrors/node-sass/](https://link.jianshu.com?t=https%3A%2F%2Fnpm.taobao.org%2Fmirrors%2Fnode-sass%2F)
   phantomjs_cdnurl=[http://npm.taobao.org/mirrors/phantomjs](https://link.jianshu.com?t=http%3A%2F%2Fnpm.taobao.org%2Fmirrors%2Fphantomjs)
   electron_mirror=[http://npm.taobao.org/mirrors/electron/](https://link.jianshu.com?t=http%3A%2F%2Fnpm.taobao.org%2Fmirrors%2Felectron%2F)
  ```

  这样，这些资源就会在`taobao`镜像下载了；

当你下载成功并再次运行`npm start`命令并成功时，你会得到这样的界面；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-01 23.36.08.png" alt="截屏2020-08-01 23.36.08" style="zoom:33%;" />

# [应用程序]进程和线程

什么是进程？维基百科这样说道：

```
an instance of a computer program that is being executed;(一个正在被执行的计算机程序实例)
```

基本上每一个应用程序就能作为一个进程；

那什么是线程呢？线程是操作系统能够进行运算调度的最小单位；它被包含在进程之中，是进程中的实际运作单位；比如百度网盘的下载线程、更新检查线程；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-01 23.43.34.png" alt="截屏2020-08-01 23.43.34" style="zoom: 33%;" />

那么进程和线程有什么区别？主要有以下几个方面的区别：

+ 内存使用方面：不同进程的分配的内存无法共享，一个进程内的不同线程可以共享内存，类似于一个车间内的工人可以共享塑材；
+ 通信机制方面：默认情况下进程之间难于互通（但还是有方法的。比如`IPC:interprocess communication`），线程由于共享内存，因此通信很方便；
+ 量级方面：线程比进程更轻量，创建更快速，占用资源更少；

# [`electron`]主进程和渲染进程

`electron`底层基于`chromium`，而`chromium`是多进程的，每一个标签页面就是一个进程，一般称为渲染进程`renderer process`，同时还有一个进程称为主进程`main process`，统领、管理、控制渲染进程的生命周期、资源分配，这样做有利于当一个渲染进程崩溃不会影响其他渲染进程和主进程，只要销毁这个崩溃的渲染进程即可；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-01 23.54.57.png" alt="截屏2020-08-01 23.54.57" style="zoom:33%;" />

我们一般讲`chrome`非常地占有资源，也是因为多进程的原因；

如下图所示，`chrome`的`Google Chrome`为主进程，`Google Chrome Helper`一般为渲染进程；

![截屏2020-08-01 23.52.57](/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-01 23.52.57.png)

`electron`几乎和`chrome`完全一致，也是`Electron`为主进程，`Electron Helper`为渲染进程；

![截屏2020-08-01 23.54.05](/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-01 23.54.05.png)

 主进程为什么为主进程？它有以下的特点；

+ 可以使用和系统对接的`Electron API`-创建菜单、上传文件等等；
+ 创建渲染进程--`renderer process`；
+ 全面支持`Node.js`，可以使用任何`Node.js`的特性；
+ 只有一个，作为整个程序的入口点；

同样地，渲染进程有以下的特点：

+ 可以有多个，每一个对应一个窗口；
+ 每一个都是一个单独的进程；
+ 全面支持`Node.js`和`DOM API`，只有渲染进程可以访问`DOM API`，一定到注意；
+ 可以使用一部分`Electron`提供的`API`；

主进程和渲染进程在可以使用的`API`上有一部分不同，但是同时也有共用的`API`；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 00.00.33.png" alt="截屏2020-08-02 00.00.33" style="zoom:33%;" />

# [`nodemon`]在`electron`项目中使用`nodemon`

由于我们经常需要在服务跑起来的时候更新代码，这时候我们的服务还是之前的代码所启动的，这时候我们就需要关掉服务重新启动，太麻烦了！我们可以使用`nodemon`来去掉这麻烦的过程，经过配置的`nodemon`会监听我们的代码变更然后自动重启服务；

首先我们安装它：

```
npm install nodemon --save-dev
```

然后在我们`package.json`的运行命令中这样写：

```
  "scripts": {
    "start": "nodemon --watch main.js --exec \"electron .\""
  },
```

这样当我们使用`npm run start`命令启动服务器后，`nodemon`就可以为我们提供对`main.js`文件的监听服务，每当我们更新了`main.js`文件会自动运行`electron .`来重启服务；

# [`node`]在`node.js`中使用`es6`

看到这个话题，自然而然想到使用`babel-node`，但是实际上，`Node.js`目前提供了实验性功能支持`es6`的语法，我们可以修改一下`node.js`的编译命令来使得`node.js`原生编译`es6`；

```
node --experimental-modules my-app.mjs
```

注意，待编译的文件后缀必须是`.mjs`，否则是不会生效的；

# [`electron`]创建窗口

`electron`只能在主进程中创建窗口，在我们之前下载的样例项目中，`main.js`就是主进程文件，我们可以在该文件中进行窗口的创建：

```js
const {
  app,
  BrowserWindow //代表一个窗口
} = require('electron')

//当我们的electron已经完成初始化，指app一切准备就绪
app.on('ready', () => {
  //当实例化一个窗口这个窗口就会显示出来，不需要调用window.display()这种函数
  let mainWindow = new BrowserWindow({
    width: 800, //指定宽度
    height: 600, //指定高度
    webPreferences: {
      //一般是指渲染进程，就是每一个单独窗口的特性
      nodeIntegration: true, //在窗口中是否允许使用node
    },
  })
  mainWindow.loadFile('index.html') //指定该窗口要加载的文件

  let secondWindow = new BrowserWindow({
    width: 400,
    height: 300,
    webPreferences: {
      //一般是指渲染进程，就是每一个单独窗口的特性
      nodeIntegration: true, //在窗口中是否允许使用node
    },
    parent: mainWindow, //指定该页面的父亲是谁，
    //这样会使得当用户拖动父窗口时子窗口连带拖动，
    //当父窗口销毁时子窗口也销毁
  })
  secondWindow.loadFile('second.html')
})
```

如上面代码所示，我们创建了两个父子窗口；

这两个所创建的窗口，都会跑在渲染进程下；

我们的父窗口打开的渲染文件是`index.html`，它长这样：

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="UTF-8">
  <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
  <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
  <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'">
  <title>Hello World!</title>
</head>

<body>
  <h1>Hello World!</h1>
  i am solingjees <span id="node-version"></span>

  <!-- You can also require other files to run in this process -->
  <script src="./renderer.js"></script>
</body>

</html>
```

然后在这个文件所依赖的`renderer.js`文件中，我们这样写来演示一些特性（注意注释内容，面试常考）：

```js
// This file is required by the index.html file and will
// be executed in the renderer process for that window.
// No Node.js APIs are available in this process because
// `nodeIntegration` is turned off. Use `preload.js` to
// selectively enable features needed in the rendering
// process.

//DOMContentLoaded
//当初始的 HTML 文档被完全加载和解析完成之后， DOMContentLoaded 事件被触发， 而无需等待样式表、 图像和子框架的完成加载。
//load
//load 仅用于检测一个完全加载的页面， 页面的html、 css、 js、 图片等资源都已经加载完之后才会触发 load 事件。

//在html文档加载解析完成后
window.addEventListener('DOMContentLoaded', () => {
    document.getElementById('node-version').innerHTML = process.versions.node
})
```

这样写是想告诉你，在我们的渲染进程中我们可以获取、使用`DOM`和`Node.js`的`API`；

# [`electron`]进程通信

`electron`每一个页面都是一个进程，而只有`main process`拥有操作某些特定`api`的能力，比如文件操作，那么渲染进程必须要和主进程通信才能完成一个指定的业务，比如我们要在一个渲染进程下载文件到机器上，那么必须要和主进程通信；

而主进程要通知渲染进程一些数据更新、界面更新，提供新的数据和发送消息同样地要进程通信；

那么不同进程如何通信？`electron`使用`IPC`在进程之间进行通讯，在不同进程间，使用在`web`上经常使用的事件来进行通信（事件驱动）；

`IPC`分成两个模块，主进程侧的`IPC`模块、渲染进程侧的`IPC`模块，也就是说在这两端都有一个负责管理通信的模块，在下面的代码中你可以发现这样的设计；

> `electron`提供了一个`devtron`插件用于增强控制台的功能，你可以进入其官网查看，但是由于该项目已经西垂暮年、无人维护，因此已经不建议安装，最新版`electron`不兼容该工具，`electron 7.1.1`版本以下可以使用；

# [`electron`]进程通信实现

进程通信实现很简单，贴代码你就能懂了；

首先是主进程的：

```js
const {
  app,
  BrowserWindow,
  ipcMain //ipc主进程侧模块
} = require('electron')
//当我们的electron已经完成初始化，指app一切准备就绪
app.on('ready', () => {
  //当实例化一个窗口这个窗口就会显示出来，不需要调用window.display()这种函数
  let mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      //一般是指渲染进程，就是每一个单独窗口的特性
      nodeIntegration: true, //在窗口中是否允许使用node
    },
  })
  mainWindow.loadFile('index.html') //指定该窗口要加载的文件
  ipcMain.on('message', (e, msg) => { //接收messgae事件
    console.log(msg)
    e.reply('reply', 'hello from main process') //你可以处理完毕后直接reply回去，同样是使用事件的方式进行发送
  })
})
```

然后是渲染进程的：

```js
const {
    ipcRenderer //ipc渲染进程侧模块
} = require('electron')

window.addEventListener('DOMContentLoaded', () => {
    document.getElementById('node-version').innerHTML = process.versions.node
    document.getElementById('send').addEventListener('click', () => {
        ipcRenderer.send('message', 'hello from renderer') //发送一个事件名为message的消息
    })
    ipcRenderer.on('reply', (e, msg) => { //接收一个事件名为reply的消息
        document.getElementById('message').innerText = msg
    })
})
```

这样我们就完成了一个基础的`electron`的`ipc`通信机制；

# [`electron`]更简单的主、渲染进程通信方式------`remote`

要一直用事件来进行主进程、渲染进程通信好麻烦啊，要那么多事件，现在`electron`提供了`remote`使得我们可以直接在渲染进程里调用主进程的方法来进行一些只有主进程才能进行的操作，但是本质上还是在主进程中进行操作的，只是简化了我们发送接收事件的操作而已，千万不要以为在渲染进程中我们可以进行除主进程外无法进行的操作，只是事件的简化！

```js
const {
    ipcRenderer,
    remote //引入remote
} = require('electron')
const {
    BrowserWindow //将主进程的窗口创建函数引入
} = remote

window.addEventListener('DOMContentLoaded', () => {
    document.getElementById('send').addEventListener('click', () => {
        let win = new BrowserWindow({ //直接在这里创建，相当于向主进程发送事件来创建一样
            width: 800,
            height: 500,
        })
        win.loadURL('https://baidu.com') //由于每一个页面都是浏览器页面，因此可以非常轻松地嵌入真实网页
    })
})
```

# [`electron`]打开控制台

`web`前端工程师没有`chrome`控制台是无法活下来的，你可以在代码中这样写来打开当前窗口的控制台用于开发中的调试；

```js
const { app, BrowserWindow } = require('electron')
const devtron = require('devtron')
//当我们的electron已经完成初始化，指app一切准备就绪
app.on('ready', () => {
  devtron.install()
  //当实例化一个窗口这个窗口就会显示出来，不需要调用window.display()这种函数
  let mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      //一般是指渲染进程，就是每一个单独窗口的特性
      nodeIntegration: true, //在窗口中是否允许使用node
    },
  })
  mainWindow.loadFile('index.html') //指定该窗口要加载的文件
  mainWindow.webContents.openDevTools() //打开控制台
```

下面是打开后的效果：

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 01.20.59.png" alt="截屏2020-08-02 01.20.59" style="zoom:50%;" />



