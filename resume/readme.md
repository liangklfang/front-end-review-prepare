#### 1.react-router与react-router-dom区别
(1)react-router: 实现了路由的核心功能。

(2)react-router-dom: 基于react-router，**加入了在浏览器运行环境下的一些功能**。例如：Link组件，会渲染一个a标签; BrowserRouter和HashRouter组件，前者使用pushState和popState事件构建路由，后者使用window.location.hash和hashchange事件构建路由。

(3)react-router-native:基于react-router，类似react-router-dom，加入了react-native运行环境下的一些功能

react-router-dom依赖react-router，所以我们使用npm安装依赖的时候，只需要安装相应环境下的库即可，**不用再显式安装react-router**。基于浏览器环境的开发，只需要安装react-router-dom；基于react-native环境的开发，只需要安装react-router-native。npm会自动解析react-router-dom包中package.json的依赖并安装。

#### 2.Vue2.0 中，“渐进式框架”和“自底向上增量开发的设计”这两个概念是什么？
总的来说，**它给你提供足够的optional，但并不主张很多required(或者说主张最少)**。

我们常说：做减法比做加法难。我觉得一个框架如果是“渐进式”的，就意味着，它本身承担了较难的做减法的部分，而留给它的使用者较简单的做加法的部分。就是说，在我们用一个工具的时候，并不是为了用其中的某一个部分，而**想办法无视或者裁剪掉其他部分（做减法），而是上手就可以用上它的大多数功能，再在需要的时候引入它的官方或第三方插件（做加法）**。这样站在使用者的视角就会更省心了，就好比给你一个空屋，通了煤水电网，有床够睡，需要什么自己添；亦或给你拎包入住，但住了一阵子这不满意那不满意。比方说，jQuery的核心对于大多数使用者都是有用的，包括selector、事件、dom操作、class操作……因此我们不太会从一引入就开始想着要裁剪它——当然从今天的眼光看来的话也许是越来越想了6。本回答来源于[知乎](https://www.zhihu.com/question/51907207)。

React，它也有一定程度的主张，它的主张主要是**函数式编程的理念**，比如说，你需要知道什么是副作用，什么是纯函数，如何隔离副作用。


#### 3.为什么React是函数式编程
##### 3.1 函数是第一等公民
这一点，JavaScript没有问题，在JavaScript的世界里，**函数可以作为参数传递，函数可以赋值给一个变量，一个函数的执行结果也可以是一个函数**，因为在JavaScript里面，函数也是对象，第一等公民的地位妥妥的。因为函数是第一等公民，所以函数的组合就成为可能，可以玩出函数式编程才有的技巧。

##### 3.2 数据是不可变的(Immutable)
在纯种的函数式编程语言中，数据是不可变的，所有的数据一旦产生，就不能改变其中的值，如果要改变，那就只能生成一个新的数据。JavaScript语言本身和Immutable沾上边的只有Object.freeze这个函数，可以“冻结”一个对象，防止对这个对象的直接修改。
```js
const obj = {name: 'Morgan'};
Object.freeze(obj)；
obj.name = 'Cheng'; //strict模式下抛出异常，非strict模式下也不会修改obj.name的值。
```
在React中，强调一个组件不能去修改传入的**prop值**，也是遵循Immutable的原则。在Redux中，更是强调Immutable的作用，每个**reducer不能够修改state**，只能返回一个新的state。Immutable是个好原则，可以让代码更容易维护，当你看到一个变量的时候，可以放心假设这个变量代表的值不会被篡改，否则，你就会很操心。

在JavaScript中，尽量不要使用push方法去修改一个数组。
```js
const arr = [1, 2, 3];
arr.push(4); //这样不好，看到这代码我就方了，需要从上往下琢磨一下arr到底存的是啥
const newArr = [...arr, 4]; //这样，arr不会被修改，很放心，要修改过的版本用newArr就好了
```
也不要直接去修改一个对象的字段。
```js
const me = {name: 'Morgan'};
me.skill = 'React'; //这样不好，拿不准me里是啥了
const newMe = {...me, skill: 'React'}; //这样，me不会被修改
```
保持**Immutable的代价就会有一些空间和时间损耗，不过说真的，对绝大部分场合，真的不用去操心这点空间损耗，代码的可读性比这点损耗重要**。

##### 3.3 强制使用纯函数
所谓纯函数，就是和数学概念上的函数一样的函数，**没有任何副作用，输出完全由输入决定**，如果这世界上绝大部分code都是纯函数，那将是一个美好的世界，因为程序运行结果更容易预测，更不容易出bug。在真正的函数式编程语言中，并不是写不出产生副作用的函数，毕竟程序运行没有输入输出那么副作用那不就没用了嘛，但是写纯函数比写不纯的函数更加方便痛快，所以还是鼓励写纯函数。

在JavaScript中，虽然我们可以通过一些手段（比如强制让this变成undefined)来避免副作用，但是JavaScript本身就是一个弱类型没有副作用检查的语言，所以没法强制使用纯函数，基本还是要靠程序员自觉。当然，我相信只要程序员充分意识到了纯函数的好处，那也不难说服他们这么写。

在**React中，组件的render函数应该是一个纯函数**，只有这样，组件渲染的结果才只和state/props有关系，遵循UI=f(state)这个公式。**在Redux，reducer必须是一个纯函数，也是函数式编程的要求**。

##### 3.4 函数只接受一个参数
这可能是函数式编程语言最让人难以理解的一个特性了，对于写了十几年任意参数个数函数的凡人，看到这个规矩真的要疯了，为啥一个函数只能有一个参数？一个函数只能有一个参数，岂不是好多功能都没法写了？让函数执行返回函数，可以把多参数的函数分解为多个单参数的函数，不过，我们还是要问：为啥要这样？

**答案，我们可以简单理解为——这就是规矩！当年邱奇发明Lambda演算时就定下的规矩！**

当然，JavaScript语言没有限制一个函数的参数个数，所以这一点上JavaScript也不够原教旨主义，不过，知道函数式编程有这样一个规矩，可以帮助我们理解为什么Redux的一些代码写成很奇怪的样子。比如，在Redux中，要写一个Middleware，代码是这个样子。
```js
const someMiddleware = store => next => action => {
  // 实现middleware
};
```
如果看得不是很清楚，可以换个形式来看，上面的代码其实就是下面这样，一个函数返回另一个函数，利用闭包(Closure)，最里面的一个函数就可以访问三个参数了。
```js
const someMiddleware = store => {
  return next => {
    return action => {
      // 实现middleware
    };
  };
};
```
理论上，既然最后的函数需要的只是store、next和action三个参数，Redux完全可以定义一个middleware的形式是这样。
```js
const someMiddleware = (store, next, action) => {
  // 实现middleware
};
```
实际上，Redux没有像上面那样做，就是遵照函数式编程的传统，Redux的作者Dan Abramov现在服务于React核心开发组，可以预见未来React也会更加“函数式”。

#### 4.fetch和ajax区别
详见[Fetch和Ajax区别](https://github.com/liangklfangl/react-article-bucket/tree/master/es6#%E9%97%AE%E9%A2%985fetch-api%E9%97%AE%E9%A2%98)。
##### 4.0 在调用response.json之前需要判断status
```js
// 这里就是Response对象
function checkStatus(response) {
  if (response.status >= 200 && response.status < 300) {
    return response;
  } else {
    // 抛出错误
    var error = new Error(response.statusText);
    error.response = response;
    throw error;
  }
}
function parseJSON(response) {
  return response.json()
}
function query(req,opt){
  return fetch(req, opt)
  .then(checkStatus)
  .catch(err => {
    console.error('fetch failed', err)
  })
  .then(parseJSON)
  .then(data => {
    console.log('fetch succeeded with JSON response, ', data);
    return data;
  });
}
```

##### 4.1 返回ReadableStream
当你的服务端返回的数据是JSON格式时，你肯定希望fetch返回给你的是一个普通JavaScript对象，然而你拿到的是一个Response对象，而真正的请求结果 —— 即response.body则是一个 ReadableStream。
```js
fetch('/api/user.json?id=2')   
// 服务端返回 {"name": "test", "age": 1} 字符串
.then((response) => {
  // 这里拿到的 response 并不是一个 {name: 'test', age: 1} 对象
  return response.json(); 
   // 将response.body通过JSON.parse转换为JS 对象
})
.then(data => {
  console.log(data); // {name: 'test', age: 1}
});
```
首先需要承认，fetch将response.body设计成ReadableStream其实是**非常有前瞻性**的，这种设计让你在请求大体积文件时变得非常有用。然而，在我们的日常使用中，还是短小的JSON 片段更加常见。而为了兼容不常见的设计，我们不得不**多一次response.json() 的调用**。

不仅是调用变得麻烦，如果你的服务端采用了严格的REST风格，对于某些特殊情况并没有返回 JSON 字符串，而是用了HTTP状态码（如：204 No Content），那么在调用response.json()时则会抛出异常。

##### 4.2 fetch 不支持取消一个请求
使用XMLHttpRequest你可以用 xhr.abort()方法取消一个请求（虽然这个方法也不是那么靠谱，同时是否真的「取消」还依赖于服务端的实现），但是**使用fetch就无能为力了，至少目前是这样的**。

##### 4.3 fetch 无法查看请求的进度
使用XMLHttpRequest你可以通过xhr.onprogress回调来动态更新请求的进度，而这一点目前**fetch 还没有原生支持**。

##### 4.4 IE不支持原生fetch
所有版本的IE均不支持原生 Fetch，fetch-ie8会自动使用XHR做polyfill。但在跨域时有个问题需要处理。

IE8, 9 的XHR不支持CORS 跨域，虽然提供XDomainRequest，但这个东西就是玩具，不支持传 Cookie！如果接口需要权限验证，还是**乖乖地使用jsonp吧，推荐使用 fetch-jsonp**。

##### 4.5 Request对象通过fetch方法发送请求
```js
const myRequest = new Request('http://localhost/flowers.jpg');
const myURL = myRequest.url; // http://localhost/flowers.jpg
const myMethod = myRequest.method; // GET
const myCred = myRequest.credentials; // omit
fetch(myRequest)
  .then(response => response.blob())
  .then(blob => {
    // function URL(){[[native code]]}
    myImage.src = URL.createObjectURL(blob);
  });
```

##### 4.6 fetch中mode: 'cors'与credentials
常用的mode属性值有:

<pre>
same-origin: 表示只请求同域. 如果你在该mode下进行的是跨域的请求的话, 那么就会报错。
no-cors: 正常的网络请求, 主要应对于后台没有设置Access-Control-Allow-Origin。话句话说, 就是用来处理script, image等的请求的。他是mode的默认值
cors: 用来发送跨域的请求. 在发送请求时, 需要带上
cors-with-forced-preflight: 这是专门针对xhr2支持出来的 preflight，会事先多发一次请求给 server，检查该次请求的合法性。
</pre>

另外, 还有一个关于cookie的跨域内容。在XHR2中,我们了解到, **withCredentials**这个属性就是用来设置在进行跨域操作时, 对不同域的Server是否发送本域的cookie，一般设置为omit(不发送)。在fetch当中, 使用的是**credentials**属性，credentials常用取值为:

<pre>
omit: 发送请求时,不带上cookie。默认值.
same-origin: 发送同域请求时,会带上 cookie.
include: 只要发送请求,都会带上 cookie.
</pre>

所以, 如果你想发送ajax时, 带上cookie, 那么你就需要使用same-origin, 如果想在跨域时也带上 cookie, 那么就需要include。


#### 5.redux-saga和redux-thunk区别
Redux-thunk和Redux-saga两者都是Redux的中间件，它们会拦截通过dispatch发送的通往store的action。进而在action到达reducer之前做一些具有副作用的操作。

##### 5.1 redux-thunk
除了拦截正常的action也能拦截特殊的thunk函数:
```js
export const thunkName =
   parameters =>
        (dispatch, getState) => {
            // Your application logic goes here
        };
```
下面是redux-thunk的代码逻辑:
```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    // 如果action是一个函数直接执行它
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }
    return next(action);
  };
}
const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;
export default thunk;
```

**redux-thunk的缺点**:thunk的缺点也是很明显的，thunk仅仅做了执行这个函数，并不在乎函数主体内是什么，也就是说thunk使得redux可以接受函数作为action，但是函数的内部可以多种多样。比如下面是一个获取商品列表的异步操作所对应的action
```js
export default ()=>(dispatch)=>{
    fetch('/api/goodList',{ //fecth返回的是一个promise
      method: 'get',
      dataType: 'json',
    }).then(function(json){
      var json=JSON.parse(json);
      if(json.msg==200){
        dispatch({type:'init',data:json.data});
      }
    },function(error){
      console.log(error);
    });
};
```
从这个具有副作用的action中，我们可以看出，函数内部极为复杂。如果**需要为每一个异步操作都如此定义一个action，显然action不易维护**。action不易维护的原因：

<pre>
I）action的形式不统一(redux-saga可以)
II）就是异步操作太为分散，分散在了各个action中(saga.js)
</pre>

##### 5.2 Redux-Saga
redux-saga通过generator函数来实现，他是ES6的新特性。通过yield命令，代码在generator中暂停并恢复执行。你可以把yield命令看做是暂停generator函数执行并返回特定值，然后后续调用者可以继续调用当前yield后代码的执行。一个generator类似于如下:
```js
function* mySaga() {
    // ...
}
```

##### 5.3 redux-saga与redux-thunk区别
Thunk不会对特定的action做出响应，而redux-saga在另一方面是注册到store中，特定的action被dispatch的时候可以让saga执行或者继续执行。


#### 6.redux-saga中[fork和spawn区别](https://redux-saga-in-chinese.js.org/docs/advanced/ForkModel.html)
你可以将saga看做是一个Graph。fork将会在**调用进程下创建一个子进程**，而spawn在当前Graph的根节点下创建一个新的子进程。当你fork一个新进程的时候，父进程将会等待forked进程完成，因此每一个异常将会从子进程冒泡到父进程，在父进程能够被捕捉到。而spawnd进程不会阻塞父进程，因此下一个yield将会立即执行，同时[父进程也无法捕捉到spawned进程抛出的异常](https://stackoverflow.com/questions/43259301/whats-the-difference-between-fork-and-spawn-in-redux-saga)。


参考资料:

[react-router和react-router-dom的区别](https://blog.csdn.net/weixin_37242696/article/details/80738392)

[React世界的函数式编程(Functional Programming)](https://zhuanlan.zhihu.com/p/26174525)

[fetch 没有你想象的那么美](https://undefinedblog.com/window-fetch-is-not-as-good-as-you-imagined/)

[传统Ajax已死，Fetch永生](https://github.com/camsong/blog/issues/2)

[前端fetch通信](https://www.villainhr.com/page/2016/09/25/%E5%89%8D%E7%AB%AF%20fetch%20%E9%80%9A%E4%BF%A1)

[从redux-thunk到redux-saga实践](https://github.com/Pines-Cheng/blog/issues/9)

[Redux-Thunk vs. Redux-Saga](https://decembersoft.com/posts/redux-thunk-vs-redux-saga/)

[异步方案选型redux-saga 和 redux-thunk（async/await）](https://blog.csdn.net/liwusen/article/details/79677827)

[构建你自己的redux-saga](https://www.yanshuo.me/p/255417)
