---
title: React笔记
date: 2019-05-20
categories:
- front-end
---

**React核心：**

- 视图渲染原理 & 生命周期state

- 组件化思想及其应用

- 事件处理

- 组件间通信



**React扩展：**

- React-router
- React-loadable
- Redux
- Reflux
- MobX


# React
## Core
###  What's React? 
React是体系、思想、状态机。
`console.dir(React);`

- Children：对象类型；
- **Component()**：父级重渲染，自身也会更新渲染；
  - setState()：**异步**渲染；
  - forceUpdate()
- Fragment：Symbol(react.fragment)；
- **PureComponent()**：只要自身props浅比较无变化，就不会更新渲染；
- StrictMode：Symbol(react.strict_mode)；
- Suspense：Symbol(react.suspense)；
- cloneElement()：
- createContext()：
- createElement()：
- createFactory()：
- createRef()：
- forwardRef()：
- isValidElement()：
- lay()：
- memo()：
- useCallBack()
- useContext()
- useDebugValue()
- useEffect()
- useImperativeHandle()
- useLayoutEffect()
- useMemo()
- useReducer()
- useRef()
- useState()
- ...

注意：React和ReactDOM其实是2个不同的东西！
### What's ReactDOM? 
`console.dir(ReactDOM);`

- createPortal()
- findDOMNode()
- flushSync()
- hydrate()
- render()
- unmountComponentAtNode(): 
- ...

## Render
**0x01:** 虚拟dom差异化算法

- Virtual Dom
- Diff Algorithm
  - What：React使用的差异算法（称为协调），使用组件标识确定是否更新现有的子对象树或丢掉现有的子树并重新挂载。
  - How：如果render函数返回的组件和之前render函数返回的组件是相同的，React就递归的比较新子对象树和旧子对象树的差异，并更新旧子对象树。如果他们不相等，就会**完全卸载掉**旧的之对象树。

**0x02:** 单向数据流渲染(Single Data Flow)

- 由上至下，通过`props`流动；
- **数据来源尽可能唯一**，否则在使用派生state的场景易报错。避坑：1.直接复制props到state上；2.如果props和state不一致便更新state；
- 组件改变？重新渲染。父组件改变？重新渲染。一部分没有导致视图改变的props改变?重新渲染。

**0x03:** 组件生命周期(Component Life Cycle) ：<http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/>

- Mounting
  - when：
    - F5
    - parent component re-mounted
  - `constructor(props)`
  - `static getDerivedStateFromProps(nextProps, prevState)`
  - `render()`
  - `componentDidMount()`
- Updating
  - when：
    - new props：父级传下的props改变时更新；注意区分`Component\PureComponent`
    - `setState()`
    - `forceUpdate()`
  - `static getDerivedStateFromProps(nextProps, prevState)`：
    - 让组件在 **props 变化**时更新 state；
    - 父组件重新渲染时，子组件必然调用；
  - `shouldComponentUpdate()`
  - `render()`
  - `getSnapshotBeforeUpdate(prevProps, prevState)`
  - `componentDidUpdate(prevProps, prevState, snapshot)`
- Unmounting：
  - when：
    - new object tree !== old object tree
  - `componentWillUnmont()`
- Error Handling：
  - when：
    - err in render、hook-function；
  - `static getDerivedStateFromError(error)`
  - `componentDidCatch(error, info)`

**0x04:** state实践理念

- 认清数据是否是本组件的state？若是，那么该组件及其子组件是该state在应用中的最小应用集合；
- 若是该数据是props传下来的，那么它是哪一个父组件的state？
- 若该数据的操作会影响视图，那么作为state；若不会，可作为组件的静态属性，但是无法直接传递至子组件，只能通过调用传递的方法对其进行操作；
- **任何数据，都要保证只有一个数据来源，而且避免直接复制它；**

## Component

**0x01:** JSX

**0x02:**组件形态及其创建方式

- Unstateful：函数式组件

  ```javascript
  function HelloComponent(props) {
      return <div>hello, {props.name}</div>
  }
  ReactDom.render(<HelloComponent name="frank" />, mountNode);
  ```

- ES6：React.Component

  ```javascript
  class HelloComponent extends React.Component {
  	constructor(props) {
  		super(props);
  	}
  	render() {
  		return <div>hello, {props.name}</div>;
  	}
  }
  ReactDom.render(<HelloComponent name="frank" />, mountNode);
  ```

- ES5：React.createElement()

**0x03:**受控组件 & 非受控组件 (**推荐使用受控组件**)

- 受控组件
  - 组件的数据由父组件传入的props控制；
  - 若有使用<u>派生state</u>，即props传入作为state；
- 非受控组件
  - 数据只保存在组件内部的state里；

**0x04:**key属性

What：React组件的属性；

How：当key变化时， React会创建一个新的而不是更新一个既有的组件。

When：

- 列表循环渲染；
- 强制让组件重新渲染；


## Communication

**0x01** 自上而下的数据流 & props：

在典型的 React 数据流中，数据是自上而下单向流动，子组件通过props获取父组件传给子组件的数据。若子组件中props数据需要修改，只能通过调用父组件方法进行修改。

**0x02** 嵌套包含 & this.props.children：

子组件可以通过this.props.children获取父组件元素内包含的子元素。

**0x03:**事件处理

**0x04** `context`上下文：先搞清楚<u>观察者模式</u>、<u>订阅/发布模式</u>、<u>生产者/消费者模式</u>！

- API：`const {Provider, Consumer} = React.createContext(defaultValue);`；
- 场景：多个层级的多个组件需要访问相同数据；请勿滥用！
- 模式：订阅者/发布者；**React 组件允许`Consumer`订阅` context` 的改变**。
  - `<Provider value={...}/>`：发布者
    - value会传递给`Provider`的后代`Consumer`；
    - Provider可嵌套使用；
  - `<Consumer/>value</Consumer>`：订阅者
    - 可以订阅`context`变化的React组件；
    - 接收一个函数作为子节点，函数接收当前 context 的值并返回一个 React 节点。传递给函数的 value将等于组件树中上层 context 的最近的 Provider 的 value属性；
- 组件树中可以有多个context

**0x05** `Refs`：提供了一种访问 render() 方法中创建的 React 元素（或 DOM 节点）的方法。

- API：
  - `React.createRef()`
  - `Callback Refs`
    - 当组件安装时，React 会将 DOM 元素传递给 ref 的回调；当组件卸载时，则会传递 null。
    - ref 回调会在 componentDidMount 和 componentDidUpdate 生命周期之前调用。
  - `String refs`：已过时...
  - `Forwarding refs`
    - 一种将 ref 通过组件传递给其子节点的技术。
    - 对于可复用组件库和高阶组件（HOC）等情况非常有用。
- 场景：
  - 与第三方DOM库集成；
  - 触发强制动画；
  - 管理焦点，文本选择或媒体播放；

```javascript
import React from 'react';

class Demo extends React.Component {
	constructor(props) {
		super(props);
		this.rf1 = React.createRef();
	}
	render() {
		return (
			<Jsx ref={this.rf1} />
			<Jsx ref={(element) => {this.rf2 = element}} />
			<Jsx ref="ref3" /> // 这种写法已过时，老项目中可能会出现
		);
	}
}


```

**0x06** 状态管理器：`Redux`、`MobX`、`Reflux`

## SSR
**0x01** what
一套代码既可以在服务端运行又可以在客户端运行，就是同构应用。服务端直出文件和客户端渲染结合，取长补短。俗称服务端渲染`SSR`。
**0x02** why

- 为了更好的性能！网络条件越差，同构优势越明显。
- SEO：服务端渲染对搜索引擎的爬取有着天然的优势。
- 兼容性

**0x03** how

## React vs Vue

**相同点：**

- 都支持服务器端渲染SSR

- 都有Virtual DOM,组件化开发,通过props参数进行父子组件数据的传递,都实现webComponent规范

- 数据驱动视图

- 都有支持native的方案,React的React native,Vue的weex

- 都有管理状态，React有redux，Vue有Vuex

**不同点：**

- React严格上只针对MVC的view层，Vue则是MVVM模式；

- virtual DOM不一样,vue会跟踪每一个组件的依赖关系,不需要重新渲染整个组件树。而对于React而言,每当应用的状态被改变时,全部组件都会重新渲染,所以react中会需要shouldComponentUpdate这个生命周期函数方法来进行控制

- 组件写法不一样, React推荐的做法是 JSX + inline style, 也就是把HTML和CSS全都写进JavaScript了,即'all in js'。Vue推荐的做法是webpack+vue-loader的单文件组件格式,即html,css,jd写在同一个文件;

- 数据绑定: vue实现了数据的双向绑定,react数据流动是单向的且是自上而下；

- state对象在react应用中不可变的,需要使用setState方法更新状态;在vue中,state对象不是必须的,数据由data属性在vue对象中管理；

# React Extend
```
// 很明显地可以看出React体系下的库间关系、功能拆分、组件化思想...
React
React-dom React-native
React-router
React-router-dom React-router-native
```

## React-router-dom
### What
- 主要组件
  `<Router>`：路由；其内部只允许有一个子元素。
  `<BrowserRouter>`：利用history API来实现的路由。
  `<HashRouter>`：利用location.hash来实现的路由。
  `<MemoryRouter>`
  `<StaticRouter>`：永远不会改变位置的Router。<StaticRouter>和react-loadable实现SSR。
  `<Route>`：当location匹配到其path属性时，渲染其对应的UI组件。
  `<Link>`：注意与<a/>的区别，点击<Link/>会导致对应的组件进行渲染而不是整个页面重新加载。
  `<NavLink>`：
  `<Switch>`：
  `<Redirect>`：重定向；会覆盖history栈顶location
  `<Promp>`

- 主要属性
  `history`
  `location`
  `match`
  `matchPath`
  `withRouter`

### How

- 路由匹配规则
- 组件及其属性的用法
```jsx
<Route path ='/midway/xxx/*' />
```

## React-loadable



## Redux
### What

redux是体小精悍，API简、功能强、可扩展性高的状态管理库。

- 组成
  - store：存状态的容器，整个应用只能有一个`store`。
  - action：把数据从应用传到`store`的有效荷载，是`store`数据的唯一来源。
  - reducer：指定了应用状态的变化如何响应`action`并发送到 `store `的。
- 三大原则
  - 单一数据源
  - state对外只读
  - 使用纯函数来执行修改
- 使用场景
  - React传统数据流无法满足复杂业务需求的中大型项目
  - 多交互
  - 多数据源
  - 组件状态需要共享，且在任何地方都能拿到，即组件状态全局性
- 特点
  - 订阅-发布思想。
  - `redux`只关心如何管理state，UI绑定交给`react-redux`。
  - 在同一个地方查询\改变\传播状态的变化。
  - web应用视作一个状态机，视图与状态是一一对应的。
  - 所有状态保存在一个对象(`state tree`)里。

### How
- `action`：
  - 等同于传统模式下，子级回调通知`this.props.callback()`。
  - 即纯声明式数据结构，只提供事件所有要素，不提供`state`更新逻辑。
  - `store.dispatch(action)`全局分发`action`，任何`reducer`只要匹配上就会执行。
- `reducer`：
  - 等同于传统模式下，父级传到子级的方法`this.callback()`。
  - 只要传入参数相同，返回计算得到的下一个 state 就一定相同（没有特殊情况、没有副作用，没有 API 请求、没有变量修改，**单纯执行计算并返回新的state**）。
  - **Don't!**：
    - 修改传入参数。
    - 执行有副作用的操作。Eg:  API 请求和路由跳转。
    - 调用非纯函数。Eg:  `Date.now()` 或 `Math.random()`。
    - 不能返回`undefined`；未匹配到action就返回接受的第一个参数，即`preState`；传入的state是`undefined`，就返回reducer对应的初始`state`。
- `store`
  - 等同于传统模式下，等同于提升后的父级中存储起来的`state`。
  - 更新state的逻辑封装到reducer中，且更新state的唯一方式是`store.dispatch(action)`。

### Plugin
- `react-redux`
  - 
- `redux-thunk`
	- 被 dispatch的function会接收dispatch作为参数，并且可以异步调用它。这类的 function 就称为`thunk`。
```jsx
/*
* 工作机制图
* 顶层分发状态，让React组件被动地渲染
* 监听事件，事件有权利回到所有状态顶层影响状态
* 仓库不能直接修改，修改只能通过派发器（dispatch）派发一个动作（action）
*/
+---------+       +---------+       +-------+
¦ Actions ¦------>¦ Rducers ¦------>¦ Store ¦
+---------+       +---------+       +-------+
     ^                                  ¦
     +----------------------------------+
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router } from 'react-router-dom';
import { createStore, applyMiddleware } from 'redux';
import { Provider, connect } from 'redux-react';

// action
const action = {
	type: 'action_type'
};

// reducer
const reducer = (preState, action) => (nextState);

// store
const logger = ({ getState }) => {
  return (next) => (action) => {
    console.log('will dispatch', action)
    let returnValue = next(action)
    console.log('state after dispatch', getState())
    return returnValue
  }
}

const store = createStore(
    reducer,
    action,
    applyMiddleware(logger)
);
store.subscribe(function(){}); // 订阅action的监听器

// view
const redux_component = connect()(react_component);
store.dispatch(action); // 分发action

// usage
ReactDOM.render(
	<Provider store={store}>
    	<App>
        	<Router>
            	...
            </Router>
        </App>
    </Provider>,
    document.getElementById('app')
);
```

### Reference
- <https://mp.weixin.qq.com/s/ODuGrIcChOYPOP5euSu6lg>
- https://github.com/reduxjs/redux

## Mobx

## Reflux
**单向数据流模式**：由actions和stores组成；
**事件驱动**：React组件更新 => 新数据作为参数传递进actions => 通过事件机制传递至stores中，stores可向服务器发起请求并更新数据数据库 => 数据更新完毕后，通过事件机制传递到对应组件中，更新UI；
```javascript
+---------+       +--------+       +-----------------+
¦ Actions ¦------>¦ Stores ¦------>¦ View Components ¦
+---------+       +--------+       +-----------------+
     ^                                      ¦
     +--------------------------------------+
```

# *Tips

**0x01**: this.setState()是异步操作。在子组件方法嵌套调用父组件方法并且涉及父组件方法更改state、子组件方法后续需要新的props进行操作时，会出现结果不可控情况。

- this.setState()提供回调函数，可在里面添加后续逻辑。
- setTimeout()将子组件后续方法放入第二执行队列。
- 可抽出公共部分放入render()。

**0x02**: 遍历生成元素要带key。数组元素中使用的key在其兄弟之间应该是独一无二的。然而，它们不需要是全局唯一的。
**0x03**: 高频率轮询组件刷新问题。继承React.Component时，只有组件的props或state发生改变都会导致re-render，刷新页面导致组件的重新挂载不等于重渲染；纯函数组件情况下，只要父组件渲染，子组件必然渲染；因为导致子组件会出现不可控情况，如滑轮重置、弹框详情丢失...注意！组件-re-render后不一定会更改DOM！因为React在render之后存在节省性能的机制，即 
	所有组件和元素都render完成 ===> 执行diff算法 ===> 修改有差异的dom
解决方案：

- 子组件继承React.PuerComponent。Rreact.PureComponent渲染前会执行props和state的浅比较（引用类型数据在此需要注意），来决定是否render这次更新以避免无用re-render。
- 数据存储在组件对象属性上而非this.state。

**0x04:** 组件间的渲染关系易混淆，涉及react数据流、渲染机制及生命周期。

- 确定组件的关系，如父子、非父子。

- 若是父子关系，
  - 首先确定，子组件的state是否依赖于传下来的props，即是否使用了<u>派生state</u>；
  - 不依赖的话，
  - 依赖的话，
- 若是非父子关系，
  - 画出组件树，找到他们共有的父组件；

**0x05:** react和vue的区别，业务开发中常常搞混机制。

# Ecology

- React组件
	- https://github.com/react-component
	- https://github.com/ant-design