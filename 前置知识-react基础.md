# [`react`]什么是`react`?

`react`是用于`view`层的一个前端框架库，它有什么特点？

+ 声明式写法，强调结果，要什么组件就把它写出来而不是写写出来的过程，而不是命令式写法强调过程；
+ 组件化，所有组件可以随意组合和粘贴；
+ 一次学习，随处编写，`web + mobile web + mobile software + pc`端到处可以写；

那么为什么要学习`react`？

+ 大厂加持，来自于`facebook`的技术和丰富的生态和广泛的开源开发者；

+ 简单易懂；

+ 最流行，使用人数最多的一项技术，来自于`2019`年的两张报告可以佐证这一点：

  <img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 02.04.10.png" alt="截屏2020-08-02 02.04.10" style="zoom:50%;" />

  ![截屏2020-08-02 02.04.24](/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 02.04.24.png)

# [`react`]配置`react`开发环境

较新的`react`对`node.js`的版本有最低要求，建议要使用命令`node -v`先检查`node.js`版本来确保兼容；

如何创建一个初始的`react`项目？`react`为我们提供了`create-react-app`这个命令来快速形成一个`react`模版项目；

它生成的`react`项目有什么特点？

+ 只有一个依赖，`create-react-app`打包生成的`react`项目已经集成了`Webpack、Babel、ESLint`等其他组件，我们不再需要手动安装这些依赖，而这些都被隐藏在了`react`的底层，只暴露出`react`这一个依赖项；
+ 零配置，开箱即用，你不需要精确地配置你的`react`项目来达到可用的目的，`creat-react-app`生成的项目提供了现代的构建配置；
+ 可配置，生成的`react`项目如果不满意需要自己配置一下，你可以简单地使用`npm eject`命令调出`webpack`的配置文件来做进一步配置；
+ 集成`build`脚本、预提供测试框架，以及更多...

如何使用该命令呢？

```
npx create-react-app my-app （node.js版本v5.2及以上提供npx）
cd my-app
npm start
```

> 什么是`npx`？`npx`提供了一种全局安装的替代方案，我们不需要在本地全局安装`create-react-app`这个包才能使用`create-react-app`命令创建项目，现在即使你没有下载`create-react-app`包你也能用它来创建一个`react`项目，但是在以前，你必须先用`npm install create-react-app -g`全局安装命令才能使用`create-react-app`；
>
> 它有什么样的优势呢？
>
> + 避免安装全局模块，对于`create-react-app`这种只会在创建项目时使用的命令根本没必要下载过来长期存储占用本地空间，直接使用`npx`可以在我们需要的时候使用；`npx`会将`create-react-app`先下载到一个临时目录，使用完毕后会将其删除掉；
> + 调用项目内部安装的模块；在我们的项目中，我们有时候需要使用`node_modules`中安装的组件所提供的命令，比如我们要使用`nodemon`启动监视，一种方法是在`package.json`中的`scripts`中添加命令来使用`node_modules`中的`nodemon`命令，比如`nodemon --watch main.js --exec electron .`，这是一种方式，但是这种方式要求我们必须在我们的`package.json`中以脚本命令的形式调用；如果我们要在我们的项目文件夹的终端中使用，可以这样调用`node_modules/.bin/nodemon ...`，这是第二种方式，这种方式要求我们将命令的路径写出来，非常麻烦；这时候`npx`大显神功，我们可以在我们的项目文件夹的终端中这样写：`npx nodemon ...`，这样会查找当前目录下的`node_modules/.bin`文件夹下的`nodemon`命令来执行；

>小贴士：
>
>为什么`package.json`的脚本命令可以直接使用`node_modules/.bin`目录下的命令？
>
>这是因为当我们使用`package.json`中的命令时使用`npm run xxx`这种命令格式，这样做会自动新建一个`shell`，这个`shell`会将`node_modules/.bin`目录添加到`path`中，这样就能直接调用`node_modules/.bin`中的命令了，`npm run `结束之后会将`path`还原；

# [`eslint`]`eslint`添加`react hooks`支持

这部分比较简单，首先我们下载`eslint、eslint-plugin-react-hooks`这两个插件；

一般我们都把`eslint`的配置都写在一个单独的文件中，我们在`.eslintrc.js`文件下这样写来支持和使用`react-hook`的语法检查和纠错：

```
module.exports = {
  env: {
    browser: true, //指定项目运行的环境，如果不写那么所有浏览器的api都会报红，表示非法
    es2020: true, //支持es2020的语法
    node: true, //支持node.js的api，如果不写，所有common.js的规范，比如module.exports语法不支持了
  },
  parserOptions: { //指定要支持的语言选项
    ecmaVersion: 11, //支持目标es版本，这里设置es11
    sourceType: 'module', 
    ecmaFeatures: { //设置其他选项
      jsx: true,  //支持jsx语法
    },
  },
  plugins: ['html', "react", 'react-hooks'], //添加第三方插件
  extends: ['eslint:recommended', 'plugin:react-hooks/recommended'], //扩展规则集
  rules: {
    "react/jsx-uses-react": 1 //使用规则
  }
}
```

之后在编写代码时，`react`组件的文件名改成`jsx`后缀能够应用`jsx`检测了，其实主要是如果写成`js`那么`eslint`的格式化结果非常难看；

# [`react`]`State Hook`

```
import React, { useState } from 'react'

const LikeButton = () => {
  //数组解构，第一个是数据，第二个是修改数据的方法
  // const [like, setLike] = useState(0) //设定初始参数
  const [obj, setObj] = useState({ like: 0, on: true })
  return (
    <>
      <button onClick={() => setObj({ like: obj.like + 1, on: true })}>
        {obj.like}👍
      </button>
      <button onClick={() => setObj({ like: 0, on: !obj.on })}>
        {obj.on ? 'IS ON' : 'IS OFF'}
      </button>
    </>
  )
}

export default LikeButton

```

# [`react`]`Effect Hook`

什么是`effect`，就是副作用，比如如果我输入一个数据输出了一个结果，但是同时还发送消息发给了其他组件，那么这个行为就可以称为副作用行为，就是指在我走正常流程过程中引发的其他事件；

`effect hook`其实就是一个监视者，它要监视数据的变化然后执行相应的逻辑，就是函数中的副作用的角色；

`effect hook`分成两种`effect`，一种是无需清除的`effect`，比如更新`dom`后触发一系列事件：发送网络请求、手动更新`dom`、记录日志等等；

在`react class`中，如果我们要在组件挂载后和组件更新后都更新一个数据，那么我们需要在两个生命周期钩子函数中编写相同的业务逻辑：

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 15.52.21.png" alt="截屏2020-08-02 15.52.21" style="zoom: 33%;" />

但是如果使用`react hooks`就不需要这么麻烦了；

```jsx
import React, { useState, useEffect } from 'react'

const LikeButton = () => {
  //数组解构，第一个是数据，第二个是修改数据的方法
  // const [like, setLike] = useState(0) //设定初始参数
  const [obj, setObj] = useState({ like: 0, on: true })
  //第一个参数是监听的回调函数，第二个参数是绑定的数据的数组，当数据更新后会执行回调函数，
  //但是当组件第一次挂载完成时一定触发一次回调函数
  //第二个参数不传递表示每一次组件绑定的数据更新都会触发一次，如果传递的数组为[]，那么只会在第一次组件挂载时触发
  useEffect(() => {
    document.title = `点击了${obj.like}次`
  }, [obj])
  return (
    <>
      <button onClick={() => setObj({ like: obj.like + 1, on: true })}>
        {obj.like}👍
      </button>
      <button onClick={() => setObj({ like: 0, on: !obj.on })}>
        {obj.on ? 'IS ON' : 'IS OFF'}
      </button>
    </>
  )
}

export default LikeButton
```

另一种是需要清除的`effect	`，比如事件监听函数，当给一个组件添加了`click`监听函数，那么我们必须要记得当组件卸载时将其删掉，否则会发生内存泄漏，这在`react class`中我们需要这样做；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 16.00.56.png" alt="截屏2020-08-02 16.00.56" style="zoom: 50%;" />

但是在`react hooks`中这变的更加简单；

```jsx
import React, { useEffect, useState } from 'react'

export default () => {
  const [positions, setPositions] = useState({ x: 0, y: 0 })
  useEffect(() => {
    console.log('wo bei chu fa l ')
    const updateMouse = (e) => {
      setPositions({ x: e.clientX, y: e.clientY })
    }
    document.addEventListener('click', updateMouse)
    //在下一次发生副作用前清除上一次的副作用，这样能保证不会出现多次监听事件的挂载
    //当组件挂载时会运行一次，当组件卸载时会清除一次
    return () => {
      console.log('wo bei shan chu l ')
      document.removeEventListener('click', updateMouse)
    }
  })
  return (
    <p>
      x : {positions.x} , y : {positions.y}
    </p>
  )
}
```

现在我们给`effect`添加所依赖的数据设定为另一个数据，提升一下复杂度；

```jsx
import React, { useState, useEffect } from 'react'
import axios from 'axios'

export default () => {
  const [url, setUrl] = useState('')
  const [loading, setLoading] = useState(false)
  const [fetch, setFetch] = useState(false)
  const style = {
    width: 200,
  }
  useEffect(() => {
    setLoading(true)
    axios.get('https://dog.ceo/api/breeds/image/random').then((res) => {
      console.log(res)
      setUrl(res.data.message)
      setLoading(false)
    })
  }, [fetch])

  return (
    <>
      {loading ? <p>🐶读取中...</p> : <img src={url} alt="dog" style={style} />}
      <button onClick={() => setFetch(!fetch)}>更新图片</button>
    </>
  )
}
```

# [`react`]自定义`hooks`

就`state、effect`等官方提供的`hook`肯定无法满足我们的要求，我们的业务千奇百怪，需要的`hook`也必须要支持定制才行；

我们知道，`hooks`就是能够使得当内外任何一个数据变化时，自动执行逻辑，来修改内外传递的数据达到一致性；

什么是钩子，就是对数据的监听并自动执行业务逻辑；基于这一点我们就可以自己设计一个`hook`了；

我们举个例子，这样能让我们更加认识到自定义`hooks`的好处；

我们现在有一个业务，就是需要实时获取当前鼠标定位；一般来讲，使用`hooks`会这样处理，直接在我们的一个组件里这样写：

```
 const [positions, setPositions] = useState({ x: 0, y: 0 })
  useEffect(() => {
    const updateMouse = (e) => {
      setPositions({ x: e.clientX, y: e.clientY })
    }
    document.addEventListener('mousemove', updateMouse)
    //在下一次发生副作用前清除上一次的副作用，这样能保证不会出现多次监听事件
    //当组件挂载时会运行一次，当组件卸载时会清除一次
    return () => {
      document.removeEventListener('mousemove', updateMouse)
    }
  },[])
```

在一个组件内这样写在语法没有任何问题；我们设定一个`state`，然后通过添加监听函数来实时修改它，然后去使用这个`position state`	就行了；但是如果我们不止一个组件要使用鼠标定位呢？这就很讨厌了，我们需要在另一个组件里编写相同的过程才能获取到实时的鼠标定位；

很明显，这种重复的代码只会增加我们的工程体积，除此之外没有任何好处；

因此自定义`hooks`应运而生，它可以将这种重复的过程剥离出来，比如下面，就是一个自定义`hooks`的封装；

一般来讲，我们会将所有的自定义`hooks`放在`src/hooks`目录下，这样更加有条理性；

```js
import React, { useState, useEffect } from 'react'

//自定义hook最好以use开头，这也是为了方便对hook的检查
const useMousePosition = () => {
  const [positions, setPositions] = useState({ x: 0, y: 0 })
  useEffect(() => {
    const updateMouse = (e) => {
      setPositions({ x: e.clientX, y: e.clientY })
    }
    document.addEventListener('mousemove', updateMouse)
    //在下一次发生副作用前清除上一次的副作用，这样能保证不会出现多次监听事件
    //当组件挂载时会运行一次，当组件卸载时会清除一次
    return () => {
      document.removeEventListener('mousemove', updateMouse)
    }
  },[])
  return positions //我们将position state传递出去
}

export default useMousePosition

```

简单一看发现没有什么大变动，只是封装成了一个函数，在实际的组件中去使用它也是和一般的函数一样；

```js
import useMousePosition from './hooks/useMousePosition'
const App = ()=>{
  ...
  const position = useMousePosition() //获取到这个钩子函数返回的state
  ...
}
export default App
```

但是实际上，这个时候传递回来的`position`数据，已经是一个实时的`state`了，当我们鼠标在移动时，钩子函数内部会将`position state`实时更新，同时还会实时将`position`的更新传递到每一个调用它的组件上，也就是说调用的组件从这个函数中获取到的值`position`也是实时更新的，是一个活动的数据，而不是静止的数据，这样就很爽了，这已经不是函数的概念了，是数据绑定的概念；

那么钩子函数和普通函数有什么区别？为什么它被认为一个钩子函数？我们可以观察它的名字：`use+xxxxx`的结构，这是`react hooks`的一个规定，所有的钩子函数都最好以`use+xxxxx`为命名的结构，否则会因为无法判断某个函数是否包含对其内部` Hook` 的调用，而导致`React` 将无法自动检查你的` Hook` 是否违反了 `[Hook 的规则](https://zh-hans.reactjs.org/docs/hooks-rules.html)`；但是如果你不是这种名字结构，也会优先认为是一个钩子函数；

# [`HOC`]`Higher order component`：高阶组件

高阶组件就是一个函数，接收一个组件作为参数，返回一个新的组件；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 20.18.55.png" alt="截屏2020-08-02 20.18.55" style="zoom:50%;" />

概念很高深，代码很简单；

起初，`react-router`在将一个开发者编写的组件封装为一个路由组件时就是使用高阶组件来完成的；

比如下图就是创建了一个高阶组件，传入一个`class`组件以及一个字符串参数`url`；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 20.21.07.png" alt="截屏2020-08-02 20.21.07" style="zoom:33%;" />

最后渲染时将传入的组件放进来并添加参数，或者嵌套进其他的组件中，最后产生一个更加复杂的高阶组件；

<img src="/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 20.22.37.png" alt="截屏2020-08-02 20.22.37" style="zoom:28%;" />

使用这个高阶组件时，我们要这样做，我们使用函数的形式来创建一个组件，是不是看上去有些迷茫；

![截屏2020-08-02 20.24.53](/Users/sulingjie/Library/Application Support/typora-user-images/截屏2020-08-02 20.24.53.png)

而且，高阶组件有时候对传入的组件有一些要求，对开发者来说需要一些精力去阅读文档才能理解要传递什么样的组件才符合创建一个高阶组件的要求，因此比较麻烦；

# [`react`]`Hook`规则

​	我们使用`hooks`需要遵循一些规则才能保证我们的`hooks`是正确的、生效的；

+ 只在最顶层使用`hook`；不在循环、条件、嵌套函数中调用`hook`，确保总是在你的`React` 函数的最顶层调用他们。遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。这让 React 能够在多次的 `useState` 和 `useEffect` 调用之间保持 hook 状态的正确。

  为什么必须要这样做呢？这实际上是因为`react hooks`的实现的原因；

  `react`使用`hooks`调用的顺序来将一个`hook`的内部`state`和一个`hook`进行对应，比如若干个`useState`，内部需要若干套不一样的内部状态，当我们使用`set`方法设置`state`时，`react`会按照这个方法所对应的顺序确定第几个内部状态是我这个`useState`配套的，然后可以进行操作；

  但是如果我们给其中一个添加条件才能创建`state`呢？假设第一次渲染时条件成立，这些内部`state`都按照顺序设置好了，每一个`useState`都对应了正确的内部`state`，即使因为组件内部数据更新导致组件重新渲染，这个保存`hooks state`的数据表也不会重新渲染，除非卸载后重新挂载；

  假设第二次时数据更新了，这次渲染时这个条件不成立了，导致一个`useState`没有发生，进而导致对应顺序的内部`state`没有对应上正确的`hook`，紧接着的`hook`会将这个没有发生的`hook`所对应的`state`作为自己的`state`，导致接下来所有的`hook`都没有接上正确的`state`，导致出现`bug`；

+ 只在`react`函数中使用`hook`，就是不要在普通函数中使用`hook`，`react`可不知道你这是不是一个`hook`；

# [`react`]`react`哲学

`react`哲学是指使用`react`进行开发的思考方式，一般分成以下几个步骤，具体参见`https://zh-hans.reactjs.org/docs/thinking-in-react.html`；

+ 将设计好的`UI`划分为组件层级；
+ 创建应用的静态版本；
+ 确定`UI state`的最小完整表示；
  + 最小化`state`；
  + `DRT - DON'T Repeat Yourself`；
  + 有些数据可以根据已有的`state`推算出；
  + 使用多个`state`变量；
+ 确定`state`放置的位置；
+ 添加反向数据流；



























