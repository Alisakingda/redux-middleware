# redux 中间件

### Redux-thunk

1. 安装

```js
npm install --save redux-thunk
```

2. 使用

   - 引入 applyMiddleware

   ```js
   import { createStore, applyMiddleware } from "redux";
   ```

   - 引入 redux-thunk 库

   ```js
   import thunk from "redux-thunk";
   ```

   - 使用增强函数

   ```js
   import { createStore, applyMiddleware, compose } from "redux";
   const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
     ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
     : compose;
   const enhancer = composeEnhancers(applyMiddleware(thunk));
   const store = createStore(reducer, enhancer); // 创建数据存储仓库
   ```

3. 用处
   > 以前的 action 是对象，现在的 action 可以是函数了，这就是 redux-thunk 带来的好处

```js
import { createStore, applyMiddleware, compose } from "redux"; //  引入createStore方法
import reducer from "./reducer";
import thunk from "redux-thunk";

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
  : compose;

const enhancer = composeEnhancers(applyMiddleware(thunk));

const store = createStore(reducer, enhancer); // 创建数据存储仓库
export default store;
```

### redux-saga

1. 安装

```js
npm install --save redux-saga
```

2. 引用并创建

```js
import createSagaMiddleware from "redux-saga"; //引入saga
const sagaMiddleware = createSagaMiddleware(); //创建saga中间件
```

```js
import { createStore, applyMiddleware, compose } from "redux"; //  引入createStore方法
import reducer from "./reducer";
//------关键代码----start-----------
import createSagaMiddleware from "redux-saga";
const sagaMiddleware = createSagaMiddleware();
//------关键代码----end-----------

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
  : compose;
//------关键代码----start-----------
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware));
//------关键代码----end-----------

const store = createStore(reducer, enhancer); // 创建数据存储仓库
export default store; //暴露出去
```

3. 新建并配置 sagas.js

```js
// sages.js
function* mySaga() {}
export default mySaga;
```

```js
// index.js store
import mySagas from "./sagas";
sagaMiddleware.run(mySagas);
```

```js
import { createStore, applyMiddleware, compose } from "redux"; //  引入createStore方法
import reducer from "./reducer";
import createSagaMiddleware from "redux-saga";
import mySagas from "./sagas";

const sagaMiddleware = createSagaMiddleware();

const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
  ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({})
  : compose;
const enhancer = composeEnhancers(applyMiddleware(sagaMiddleware));
const store = createStore(reducer, enhancer);
sagaMiddleware.run(mySagas);

export default store;
```

完整的 sagas.js

```js
import { takeEvery, put } from "redux-saga/effects";
import { GET_MY_LIST } from "./actionTypes";
import { getListAction } from "./actionCreatores";
import axios from "axios";

//generator函数
function* mySaga() {
  //等待捕获action
  yield takeEvery(GET_MY_LIST, getList);
}

function* getList() {
  const res = yield axios.get(
    "https://www.easy-mock.com/mock/5cfcce489dc7c36bd6da2c99/xiaojiejie/getList"
  );
  const action = getListAction(res.data);
  yield put(action);
}

export default mySaga;
```

### React-Redux

1. 安装

```js
npm install --save react-redux
```

2. <Provider>提供器讲解

```js
import React from "react";
import ReactDOM from "react-dom";
import TodoList from "./TodoList";
//---------关键代码--------start
import { Provider } from "react-redux";
import store from "./store";
//声明一个App组件，然后这个组件用Provider进行包裹。
const App = (
  <Provider store={store}>
    <TodoList />
  </Provider>
);
//---------关键代码--------end
ReactDOM.render(App, document.getElementById("root"));
```

3. <connect>连接器讲解

```js
import React, { Component } from "react";
import store from "./store";
import { connect } from "react-redux";

class TodoList extends Component {
  constructor(props) {
    super(props);
    this.state = store.getState();
  }
  render() {
    return (
      <div>
        <div>
          <input
            value={this.props.inputValue}
            onChange={this.props.inputChange}
          />
          <button onClick={this.props.clickButton}>提交</button>
        </div>
        <ul>
          {this.props.list.map((item, index) => {
            return <li key={index}>{item}</li>;
          })}
        </ul>
      </div>
    );
  }
}
const stateToProps = state => {
  return {
    inputValue: state.inputValue,
    list: state.list
  };
};

const dispatchToProps = dispatch => {
  return {
    inputChange(e) {
      let action = {
        type: "change_input",
        value: e.target.value
      };
      dispatch(action);
    },
    clickButton() {
      let action = {
        type: "add_item"
      };
      dispatch(action);
    }
  };
};
export default connect(stateToProps, dispatchToProps)(TodoList);
// 不用写bind(this);
```

> 理解一下最后一句话代码的意思

```js
export default connect(stateToProps, dispatchToProps)(TodoList);
```

> connect 的作用是把 UI 组件（无状态组件）和业务逻辑代码的分开，然后通过 connect 再链接到一起，让代码更加清晰和易于维护。

---

> connect 的作用是把 UI 组件（无状态组件）和业务逻辑代码的分开，然后通过 connect 再链接到一起，让代码更加清晰和易于维护。

---

> connect 的作用是把 UI 组件（无状态组件）和业务逻辑代码的分开，然后通过 connect 再链接到一起，让代码更加清晰和易于维护。

---
