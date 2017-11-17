---
layout: post
title:  "Redux笔记"
tags:
  - JS
  - JavaScript
---


# Redux笔记


## 安装稳定版：

```
npm install --save redux
```

## 多数情况下，你还需要使用 React 绑定库和开发者工具。

```
npm install --save react-redux
npm install --save-dev redux-devtools
```


应用中所有的 state 都以一个对象树的形式储存在一个单一的 store 中。
惟一改变 state 的办法是触发 action，一个描述发生什么的对象。
为了描述 action 如何改变 state 树，你需要编写 reducers。


```
import { createStore } from 'redux';

/**
 * 这是一个 reducer，形式为 (state, action) => state 的纯函数。
 * 描述了 action 如何把 state 转变成下一个 state。
 *
 * state 的形式取决于你，可以是基本类型、数组、对象、
 * 甚至是 Immutable.js 生成的数据结构。惟一的要点是
 * 当 state 变化时需要返回全新的对象，而不是修改传入的参数。
 *
 * 下面例子使用 `switch` 语句和字符串来做判断，但你可以写帮助类(helper)
 * 根据不同的约定（如方法映射）来判断，只要适用你的项目即可。
 */
function counter(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1;
  case 'DECREMENT':
    return state - 1;
  default:
    return state;
  }
}

// 创建 Redux store 来存放应用的状态。
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 惟一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch({ type: 'INCREMENT' });
// 1
store.dispatch({ type: 'INCREMENT' });
// 2
store.dispatch({ type: 'DECREMENT' });
// 1
```


你应该把要做的修改变成一个普通对象，这个对象被叫做 action，而不是直接修改 state。然后编写专门的函数来决定每个 action 如何改变应用的 state，这个函数被叫做 reducer。


Redux 没有 Dispatcher 且不支持多个 store。相反，只有一个单一的 store 和一个根级的 reduce 函数（reducer）。

随着应用不断变大，你应该把根级的 reducer 拆成多个小的 reducers，分别独立地操作 state 树的不同部分，而不是添加新的 stores。



Redux 本身很简单。
当使用普通对象来描述应用的 state 时。例如，todo 应用的 state 可能长这样：


```
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
```

要想更新 state 中的数据，你需要发起一个 action。Action 就是一个普通 JavaScript 对象（注意到没，这儿没有任何魔法？）用来描述发生了什么。下面是一些 action 的示例：

```
{ type: 'ADD_TODO', text: 'Go to swimming pool' }
{ type: 'TOGGLE_TODO', index: 1 }
{ type: 'SET_VISIBILITY_FILTER', filter: 'SHOW_ALL' }
```



reducer 只是一个接收 state 和 action，并返回新的 state 的函数。 对于大的应用来说，可能很难开发这样的函数，所以我们编写很多小函数来分别管理 state 的一部分：


```
function visibilityFilter(state = 'SHOW_ALL', action) {
  if (action.type === 'SET_VISIBILITY_FILTER') {
    return action.filter;
  } else {
    return state;
  }
}

function todos(state = [], action) {
  switch (action.type) {
  case 'ADD_TODO':
    return state.concat([{ text: action.text, completed: false }]);
  case 'TOGGLE_TODO':
    return state.map((todo, index) =>
      action.index === index ?
        { text: todo.text, completed: !todo.completed } :
        todo
   )
  default:
    return state;
  }
}
```

再开发一个 reducer 调用这两个 reducer，进而来管理整个应用的 state：

```
function todoApp(state = {}, action) {
  return {
    todos: todos(state.todos, action),
    visibilityFilter: visibilityFilter(state.visibilityFilter, action)
  };
}
```




## 三大原则

### 单一数据源
整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。

```
console.log(store.getState())

/* 输出
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
*／
```

### State 是只读的
唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。

```
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
})
```

### 使用纯函数来执行修改
为了描述 action 如何改变 state tree ，你需要编写 reducers。

```

function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```





## <Provider store>


<Provider store> 使组件层级中的 connect() 方法都能够获得 Redux store。正常情况下，你的根组件应该嵌套在 <Provider> 中才能使用 connect() 方法。


```
ReactDOM.render(
  <Provider store={store}>
    <MyRootComponent />
  </Provider>,
  rootEl
)
```

```
ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      <Route path="/" component={App}>
        <Route path="foo" component={Foo}/>
        <Route path="bar" component={Bar}/>
      </Route>
    </Router>
  </Provider>,
  document.getElementById('root')
)
```


## connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])

连接 React 组件与 Redux store。

连接操作不会改变原来的组件类。
反而返回一个新的已与 Redux store 连接的组件类。


[mapStateToProps(state, [ownProps]): stateProps] (Function): 如果定义该参数，组件将会监听 Redux store 的变化。任何时候，只要 Redux store 发生改变，mapStateToProps 函数就会被调用。

该回调函数必须返回一个纯对象，这个对象会与组件的 props 合并。

如果指定了该回调函数中的第二个参数 ownProps，则该参数的值为传递到组件的 props，而且只要组件接收到新的 props，mapStateToProps 也会被调用


[mapDispatchToProps(dispatch, [ownProps]): dispatchProps] (Object or Function): 如果传递的是一个对象，那么每个定义在该对象的函数都将被当作 Redux action creator，对象所定义的方法名将作为属性名；

每个方法将返回一个新的函数，函数中dispatch方法会将action creator的返回值作为参数执行。这些属性会被合并到组件的 props 中。

如果传递的是一个函数，该函数将接收一个 dispatch 函数，然后由你来决定如何返回一个对象，这个对象通过 dispatch 函数与 action creator 以某种方式绑定在一起（


如果你省略这个 mapDispatchToProps 参数，默认情况下，dispatch 会注入到你的组件 props 中。如果指定了该回调函数中第二个参数 ownProps，该参数的值为传递到组件的 props，而且只要组件接收到新 props，mapDispatchToProps 也会被调用


[mergeProps(stateProps, dispatchProps, ownProps): props] (Function): 如果指定了这个参数，mapStateToProps() 与 mapDispatchToProps() 的执行结果和组件自身的 props 将传入到这个回调函数中。该回调函数返回的对象将作为 props 传递到被包装的组件中。你也许可以用这个回调函数，根据组件的 props 来筛选部分的 state 数据，或者把 props 中的某个特定变量与 action creator 绑定在一起。如果你省略这个参数，默认情况下返回 

```
Object.assign({}, ownProps, stateProps, dispatchProps) 
```
的结果。


[options] (Object) 如果指定这个参数，可以定制 connector 的行为。

[pure = true] (Boolean): 如果为 true，connector 将执行 shouldComponentUpdate 并且浅对比 mergeProps 的结果，避免不必要的更新，前提是当前组件是一个“纯”组件，它不依赖于任何的输入或 state 而只依赖于 props 和 Redux store 的 state。默认值为 true。
[withRef = false] (Boolean): 如果为 true，connector 会保存一个对被包装组件实例的引用，该引用通过 getWrappedInstance() 方法获得。默认值为 false。




备注

函数将被调用两次。第一次是设置参数，第二次是组件与 Redux store 连接：

```
connect(mapStateToProps, mapDispatchToProps, mergeProps)(MyComponent)
```




## 启用Redux DevTools Extension

https://github.com/zalmoxisus/redux-devtools-extension


```
const store = createStore(
  reducer, /* preloadedState, */
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
);
```

```
const store = createStore(
  reducer,
  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__(),
  applyMiddleware(...middleware)
)
```

```
import { createStore, applyMiddleware, compose } from 'redux';

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const store = createStore(reducer, /* preloadedState, */ composeEnhancers(
  applyMiddleware(...middleware)
));
  ```



谨记 reducer 一定要保持纯净。只要传入参数相同，返回计算得到的下一个 state 就一定相同。没有特殊情况、没有副作用，没有 API 请求、没有变量修改，单纯执行计算。



```
// 每次 state 更新时，打印日志
// 注意 subscribe() 返回一个函数用来注销监听器

let unsubscribe = store.subscribe(() =>{
  console.log( store.getState() );
});
```




```
function todoApp(state = initialState, action) {
  switch (action.type) {
    case SET_VISIBILITY_FILTER:
      return Object.assign({}, state, {
        visibilityFilter: action.filter
      })
    case ADD_TODO:
    case TOGGLE_TODO:
      return Object.assign({}, state, {
        todos: todos(state.todos, action)
      })
    default:
      return state
  }
}
```


```
function todoApp(state = {}, action) {
  return {
    visibilityFilter: visibilityFilter(state.visibilityFilter, action),
    todos: todos(state.todos, action)
  }
}
```



```
              展示组件(components)         容器组件(containers)
作用           描述如何展现（骨架、样式）     描述如何运行（数据获取、状态更新）
直接使用Redux   否                         是
数据来源        props                      监听 Redux state
数据修改        从 props 调用回调函数         向 Redux 派发 actions
调用方式        手动                        通常由 React Redux 生成
```


大部分的组件都应该是展示型的，但一般需要少数的几个容器组件把它们和 Redux store 连接起来。

技术上讲你可以直接使用 store.subscribe() 来编写容器组件。但不建议这么做因为就无法使用 React Redux 带来的性能优化。
也因此，不要手写容器组件，都是使用 React Redux 的 connect() 方法来生成，







```
const mapStateToProps = (state) => {
  return {
    todos: getVisibleTodos(state.todos, state.visibilityFilter)
  }
}
const mapDispatchToProps = (dispatch) => {
  return {
    onTodoClick: (id) => {
      dispatch(toggleTodo(id))
    }
  }
}
```


```
const mapStateToProps = (state) => ({
  todos: getVisibleTodos(state.todos, state.visibilityFilter)
})

const mapDispatchToProps = {
  onTodoClick: toggleTodo
}
```


使用指定的 React Redux 组件 <Provider> 来 魔法般的 让所有容器组件都可以访问 store，而不必显示地传递它。只需要在渲染根组件时使用即可。


```
import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import todoApp from './reducers'
import App from './components/App'

let store = createStore(todoApp)

render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```




## 调用异步 API 

一般情况下，每个 API 请求都需要 dispatch 至少三种 action：

一种通知 reducer 请求开始的 action。
对于这种 action，reducer 可能会切换一下 state 中的 isFetching 标记。以此来告诉 UI 来显示加载界面。

一种通知 reducer 请求成功的 action。
对于这种 action，reducer 可能会把接收到的新数据合并到 state 中，并重置 isFetching。UI 则会隐藏加载界面，并显示接收到的数据。

一种通知 reducer 请求失败的 action。
对于这种 action，reducer 可能会重置 isFetching。另外，有些 reducer 会保存这些失败信息，并在 UI 里显示出来。




为了区分这三种 action，可能在 action 里添加一个专门的 status 字段作为标记位：

```
{ type: 'FETCH_POSTS' }
{ type: 'FETCH_POSTS', status: 'error', error: 'Oops' }
{ type: 'FETCH_POSTS', status: 'success', response: { ... } }
```


```
{ type: 'FETCH_POSTS_REQUEST' }
{ type: 'FETCH_POSTS_FAILURE', error: 'Oops' }
{ type: 'FETCH_POSTS_SUCCESS', response: { ... } }
```







异步 action 创建函数对于做服务端渲染非常方便。
你可以创建一个 store，dispatch 一个异步 action 创建函数，
这个 action 创建函数又 dispatch 另一个异步 action 创建函数来为应用的一整块请求数据，
同时在 Promise 完成和结束时才 render 界面。
然后在 render 前，store 里就已经存在了需要用的 state。



像 redux-thunk 或 redux-promise 这样支持异步的 middleware 都包装了 store 的 dispatch() 方法，以此来让你 dispatch 一些除了 action 以外的其他内容，例如：函数或者 Promise。你所使用的任何 middleware 都可以以自己的方式解析你 dispatch 的任何内容，并继续传递 actions 给下一个 middleware。

当 middleware 链中的最后一个 middleware 开始 dispatch action 时，这个 action 必须是一个普通对象。这是 同步式的 Redux 数据流 开始的地方（译注：这里应该是指，你可以使用任意多异步的 middleware 去做你想做的事情，但是需要使用普通对象作为最后一个被 dispatch 的 action ，来将处理流程带回同步方式）。






## 中间件


```
const logger = store => next => action => {
  console.log('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  return result
}

const crashReporter = store => next => action => {
  try {
    return next(action)
  } catch (err) {
    console.error('Caught an exception!', err)
    Raven.captureException(err, {
      extra: {
        action,
        state: store.getState()
      }
    })
    throw err
  }
}
```


```
function applyMiddleware(store, middlewares) {
  middlewares = middlewares.slice()
  middlewares.reverse()

  let dispatch = store.dispatch
  middlewares.forEach(middleware =>
    dispatch = middleware(store)(dispatch)
  )

  return Object.assign({}, store, { dispatch })
}
```



```
import { createStore, combineReducers, applyMiddleware } from 'redux'

let todoApp = combineReducers(reducers)
let store = createStore(
  todoApp,
  // applyMiddleware() 告诉 createStore() 如何处理中间件
  applyMiddleware(logger, crashReporter)
)
```




params 是一个包含 url 中所有指定参数的对象。 

```
    <FilterLink filter="all">
      All
    </FilterLink>
```

```
const App = ({ params }) => {
  return (
    <div>
      <AddTodo />
      <VisibleTodoList
        filter={params.filter || 'all'}
      />
      <Footer />
    </div>
  );
};
```





假设有个设计师看完我们的原型之后回来说，我们最多只允许三个 todo 。我们可以使用 redux-thunk 中间件，并添加一个提前退出，把我们的 action creator 重写成回调形式：


```
function addTodoWithoutCheck(text) {
  return {
    type: 'ADD_TODO',
    text
  };
}

export function addTodo(text) {
  // Redux Thunk 中间件允许这种形式
  // 在下面的 “异步 Action Creators” 段落中有写
  return function (dispatch, getState) {
    if (getState().todos.length === 3) {
      // 提前退出
      return;
    }

    dispatch(addTodoWithoutCheck(text));
  }
}
```




```
import * as actions from '../actions'

const ConnectedNode = connect(mapStateToProps, actions)(Node)
export default ConnectedNode
```




reducer: 任何符合 (state, action) -> newState 格式的函数（即，可以用做 Array.reducer 参数的任何函数）。

root reducer: 通常作为 createStore 第一个参数的函数。他是唯一的一个在所有的 reducer 函数中必须符合 (state, action) -> newState 格式的函数。

slice reducer: 一个负责处理状态树中一块切片数据的函数，通常会作为 combineReducers 函数的参数。

case function: 一个负责处理特殊 action 的更新逻辑的函数。可能就是一个 reducer 函数，也可能需要其他参数才能正常工作。

higher-order reducer: 一个以 reducer 函数作为参数，且/或返回一个新的 reducer 函数的函数（比如： combineReducers, redux-undo）。






```
import {combineReducers, createStore} from "redux";

// 将 default import 进来的名称重命名为任何我们想要的名称。我们也可以重命名 import 进来的名称。
import defaultState, {firstNamedReducer, secondNamedReducer as secondState} from "./reducers";

const rootReducer = combineReducers({
    defaultState,                   // key 的名称和 default export 的名称一样
    firstState : firstNamedReducer, // key 的名字是单独取的，而不是变量的名字
    secondState,                    // key 的名称和已经被重命名过的 export 的名称一样
});

const reducerInitializedStore = createStore(rootReducer);
console.log(reducerInitializedStore.getState());
// {defaultState : 0, firstState : 1, secondState : 2}
```




## 范式化


```
{
    entities: {
        authors : { byId : {}, allIds : [] },
        books : { byId : {}, allIds : [] },
        authorBook : {
            byId : {
                1 : {
                    id : 1,
                    authorId : 5,
                    bookId : 22
                },
                2 : {
                    id : 2,
                    authorId : 5,
                    bookId : 15,
                }
                3 : {
                    id : 3,
                    authorId : 42,
                    bookId : 12
                }
            },
            allIds : [1, 2, 3]

        }
    }
}
```




这里有一些将怎样的数据放入 Redux 的经验法则：

应用的其他部分是否关心这个数据？
是否需要根据需要在原始数据的基础上创建衍生数据？
相同的数据是否被用作驱动多个组件？
能否将状态恢复到特定时间点（在时光旅行调试的时候）？
是否要缓存数据（比如：数据存在的情况下直接去使用它而不是重复去请求他）？







如果在执行 connect() 时没有指定 mapDispatchToProps 方法，React Redux 默认将 dispatch 作为 prop 传入。
所以当你指定方法时， dispatch 将 不 会自动注入。
如果你还想让其作为 prop，需要在 mapDispatchToProps 实现的返回值中明确指出。


如果你定义了一个 action 创建函数，调用它并不会自动 dispatch 这个 action。
















































