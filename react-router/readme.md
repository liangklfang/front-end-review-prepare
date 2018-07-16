#### 1.react-router各种路由方式比较
##### 1.1 browserHistory
Browser history是使用 React Router的应用`推荐的history`。它使用浏览器中的History API用于处理URL，创建一个像example.com/some/path这样真实的URL 。

##### 1.2 hashHistory
像这样 ?\_k=ckuvup没用的在URL 中是什么？当一个 history 通过应用程序的push或replace跳转时，它可以在新的location中存储 “location state” 而不显示在URL中，这就像是在一个HTML中post的表单数据。

在DOM API中，这些hash history通过window.location.hash = newHash很简单地被用于跳转，**且不用存储它们的location state**。但我们想全部的history都能够使用location state，因此我们要为每一个location创建一个`唯一的key，并把它们的状态存储在session storage 中`。当访客点击“后退”和“前进”时，我们就会有一个机制去恢复这些location state。

##### 1.3 createMemoryHistory
Memory history不会在地址栏被操作或读取。这就解释了我们是如何实现服务器渲染的。同时它也非常适合测试和其他的渲染环境（像 React Native ）。和另外两种history的一点不同是你必须创建它，这种方式便于测试。
```js
const history = createMemoryHistory(location)
```

##### 1.4 hashHistory和browserHistory区别
(1)browserHistory**需要服务端支持**，因为请求是直接发送到服务端的

(2) hash部分并不会被浏览器发送到服务端，也就是说不管是请求http://domain.com/index.html#foo 还是 http://domain.com/index.html#bar ，服务只知道请求了index.html并**不知道 hash部分的细节**。比如下面的一个场景:

> 比如你访问一个需要提前登录的页面。这时候，在用户被重定向到独立的网站授权之前，你的服务端应用需要告诉授权方在成功授权之后需要跳转到的URL(一般都是跳转到授权页面之前访问的页面)。如果你使用的hashHistory，那么服务端仅仅获取到#标志之前URL部分，因此此时就重定向到应用的主页面，而不是用户通过#指定的某一个下级页面。

(3)因为有些应该会**忽略URL中的hash部分**，记得之前将URL使用微信分享时会丢失hash部分

#### 2.IndexLink
```js
<Route path="/" component={App}>
  ＜IndexRedirect to="/welcome" \/>
  <Route path="welcome" component={Welcome} \/>
  <Route path="about" component={About} \/>
<\/Route>
```
**如果链接到根路由/，不要使用Link组件，而要使用IndexLink组件**这是因为对于根路由来说，`activeStyle和activeClassName`会失效，或者说总是生效，因为/会匹配任何子路由。而IndexLink组件会使用路径的精确匹配。
```js
<IndexLink to="/" activeClassName="active">
  Home
<\/IndexLink>
```
上面代码中，根路由只会在精确匹配时，才具有activeClassName。另一种方法是使用Link组件的**onlyActiveOnIndex**属性，也能达到同样效果。
```js
<Link to="/" activeClassName="active" onlyActiveOnIndex={true}>
  Home
<\/Link>
```
实际上，IndexLink就是对Link组件的onlyActiveOnIndex属性的包装。

#### 3.React-Router动态路由匹配
```js
const CourseRoute = {
  path: 'course/:courseId',
  getChildRoutes(location, callback) {
    require.ensure([], function (require) {
      callback(null, [
        require('./routes/Announcements'),
        require('./routes/Assignments'),
        require('./routes/Grades'),
      ])
    })
  },
  //{this.props.children || <Home/>}
  // 相当于IndexRouter
  // http://www.ruanyifeng.com/blog/2016/05/react_router.html
  // https://github.com/ReactTraining/react-router/issues/3232
  // 访问/的时候会实例化getIndexRoute
  getIndexRoute(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Index'))
    })
  },
  // 加载components
  getComponents(location, callback) {
    require.ensure([], function (require) {
      callback(null, require('./components/Course'))
    })
  }
}
```

#### 4.跳转前确认
React Router提供一个**routerWillLeave**生命周期钩子，这使得 React组件可以拦截正在发生的跳转，或在离开route前提示用户。routerWillLeave返回值有以下两种：
<pre>
return false 取消此次跳转
return 返回提示信息，在离开route前提示用户进行确认。
</pre>
你可以在route组件中引入Lifecycle mixin来安装这个钩子。
```js
import { Lifecycle } from 'react-router'
const Home = React.createClass({
  // 假设 Home 是一个 route 组件，它可能会使用
  // Lifecycle mixin 去获得一个 routerWillLeave 方法。
  mixins: [ Lifecycle ],
  routerWillLeave(nextLocation) {
    if (!this.state.isSaved)
      return 'Your work is not saved! Are you sure you want to leave?'
  },
  // ...
})
```

#### 5.React-Router中的components配置
```js
const App = ({ content, sidebar }) => (
  <div>
    <div className="Sidebar">
      {sidebar || <IndexSidebar />}
    </div>
    <div className="Content">
      {content || <Index />}
    </div>
  </div>
)
render((
  <Router history={withExampleBasename(browserHistory, __dirname)}>
    <Route path="/" component={App}>
      {/*App组件有默认的Category和CategorySidebar组件用于实例化*/}
      <Route path="category/:category" components={{ content: Category, sidebar: CategorySidebar }}>
        <Route path=":item" component={Item} />
      </Route>
    </Route>
  </Router>
), document.getElementById('app'))
```
当你访问的路由是/的时候就会实例化这个App组件，这个组件会接受到content和sidebar分别表示要实例化的子组件。假如我们的App组件的实例化子组件是可插拔的,这时候我们可能采用下面这种方式:
```js
<App main={<Users />} sidebar={<UsersSidebar />} \/>
```
component的传入值只能为具体的组件;components的**传入值可以为对象或具体的组件**。

##### 5.React-router实现内部跳转
```js
 static contextTypes = {
    router: PropTypes.object.isRequired
  }
  handleSearch = (value) => {
    const { router } = this.context;
    this.setState({
      inputValue: '',
    }, () => {
      if (value.indexOf("tv") == 0) {
        router.push({ pathname: `/detail/${value.slice(2)}/`});
      }else{
        router.push({ pathname: `/components/${value.toLowerCase()}/`});
     } 
    });
  }
```

##### 6.React-router的match方法与[服务端渲染](https://github.com/liangklfangl/react-article-bucket/blob/master/react-router/renderProps.md)
```js
match({ history, routes: getRoutes(store), location: req.originalUrl }, (error, redirectLocation, renderProps) => {})
```
其中完整的renderProps属性包含**routes,params,location,components,router,matchContext**属性。







参考资料:

[react router为什么推荐使用browserHistory而不推荐hashHistory？](http://phpstudy.php.cn/c.php/108189.html)

[react-router: Why the preference for browserHistory over hashHistory?](https://stackoverflow.com/questions/35015155/react-router-why-the-preference-for-browserhistory-over-hashhistory/42157741#42157741)
