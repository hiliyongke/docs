### 文章目录

-   - [前言](https://blog.csdn.net/m0_68324632/article/details/128846249#_1)
    
    - [认识Redux Toolkit](https://blog.csdn.net/m0_68324632/article/details/128846249#Redux_Toolkit_6)
    
    - [Redux Toolkit使用方法](https://blog.csdn.net/m0_68324632/article/details/128846249#Redux_Toolkit_32)
    
    -   -   [1、创建reducer](https://blog.csdn.net/m0_68324632/article/details/128846249#1reducer_34)
        -   -   [案例](https://blog.csdn.net/m0_68324632/article/details/128846249#_65)
        -   [2、创建store](https://blog.csdn.net/m0_68324632/article/details/128846249#2store_98)
        -   -   [案例](https://blog.csdn.net/m0_68324632/article/details/128846249#_108)
        -   [3、在组件中使用](https://blog.csdn.net/m0_68324632/article/details/128846249#3_132)
        
    - [Redux Toolkit的异步操作](https://blog.csdn.net/m0_68324632/article/details/128846249#Redux_Toolkit_183)
    
    -   -   - [extraReducer的另外一种写法](https://blog.csdn.net/m0_68324632/article/details/128846249#extraReducer_220)
            
            - [案例](https://blog.csdn.net/m0_68324632/article/details/128846249#_232)
            
              

## 认识[Redux](https://so.csdn.net/so/search?q=Redux&spm=1001.2101.3001.7020) Toolkit

◼ **Redux Toolkit 是官方推荐的编写 Redux 逻辑的方法。**

  在我们学习Redux的时候应该已经发现，redux的编写逻辑过于的繁琐和麻烦。

  并且代码通常分拆在多个文件中（虽然也可以放到一个文件管理，但是代码量过多，不利于管理）；

  `Redux Toolkit`包旨在成为编写Redux逻辑的标准方式，从而解决上面提到的问题；

  在很多地方为了称呼方便，也将之称为“RTK”；



◼ **安装Redux Toolkit：**

 `npm install @reduxjs/toolkit react-redux`

◼ **Redux Toolkit的核心API主要是如下几个：**

  configureStore：包装createStore以提供简化的配置选项和良好的默认值。它可以自动组合你的 slice reducer，添加你提供的任何 Redux 中间件，redux-thunk默认包含，并启用 Redux DevTools Extension。

  createSlice：接受reducer函数的对象、切片名称和初始状态值，并自动生成切片reducer，并带有相应的actions。

  createAsyncThunk: 接受一个动作类型字符串和一个返回承诺的函数，并生成一个pending/fulfilled/rejected基于该承诺分派动作类型的 thunk

## Redux [Toolkit](https://so.csdn.net/so/search?q=Toolkit&spm=1001.2101.3001.7020)使用方法

### 1、创建reducer

**◼通过createSlice创建一个slice(切片)。**

◼ **createSlice主要包含如下几个参数：**

◼ name：用户标记slice的名词

  在之后的redux-devtool中会显示对应的名词；

◼ initialState：初始化值

  第一次初始化时的值；

◼ reducers：相当于之前的reducer函数

  对象类型，并且可以添加很多的函数；

  函数类似于redux原来reducer中的一个case语句；

  函数的参数：

 ✓ 参数一：state

 ✓ 参数二：调用这个action时，传递的action参数；

◼ **createSlice返回值是一个对象，包含所有的actions；**



#### 案例

\-src

—store

\------- features

\------------- counter.js

```js
import { createSlice } from '@reduxjs/toolkit'

const counterSlice = createSlice({
  name: "counter",
  initialState: {
    counter: 666
  },
  reducers: {
    addNumber(state, { payload }) {
      state.counter += payload
    },
    subNumber(state, { payload }) {
      state.counter -= payload
    }
  }
})

export const { addNumber, subNumber } = counterSlice.actions

export default counterSlice.reducer
```

### 2、创建store

◼ **configureStore用于创建store对象，常见参数如下：**

  reducer，将slice中的reducer可以组成一个对象传入此处；

  middleware：可以使用参数，传入其他的中间件；

  devTools：是否配置devTools工具，默认为true；

#### 案例

\-src

—store

\-------index.js

```js
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from './features/counter'


const store = configureStore({
  reducer: {
    counter: counterReducer,
  }
})

export default store
```

### 3、在组件中使用

◼ **connect高阶组件把redux映射到组件的props里**

\-src

—pages

\-------Home.jsx

```js
import React, { PureComponent } from "react";
import { connect } from "react-redux";
import { addNumber } from "../store/features/counter";

export class Home extends PureComponent {
  addNumber(num) {
    this.props.addNumber(num)
  }
  render() {
    const { counter } = this.props;
    return (
      <div>
        <h2>Home Counter: {counter}</h2>
        <div>
          <button onClick={e =>  this.addNumber(5)}>+5</button>
          <button onClick={e =>  this.addNumber(8)}>+8</button>
        </div>
      </div>
    );
  }
}

const mapStateToProps = (state) => {
  return {
    counter: state.counter.counter,
  };
};

const mapDispatchToProps = (dispatch) => {
  return{
    addNumber(num) {
      dispatch(addNumber(num))
    },
  }
}

export default  ,mapDispatchToProps)(Home);

```

## Redux Toolkit的异步操作

◼ **Redux Toolkit默认已经给我们继承了Thunk相关的功能：createAsyncThunk**

```js
export const fetchHomeMultidataAction = createAsyncThunk("fetch/homemultidata", async () => {
  const res = await axios.get("http://123.207.32.32:8000/home/multidata")
  return res.data
})
```

◼ **当createAsyncThunk创建出来的action被dispatch时，会存在三种状态：**

  pending：action被发出，但是还没有最终的结果；

  fulfilled：获取到最终的结果（有返回值的结果）；

  rejected：执行过程中有错误或者抛出了异常；

◼ **我们可以在createSlice的entraReducer中监听这些结果：**

```js
 extraReducers:{
    [fetchHomeMultidataAction.pending](state,action) {
      console.log("pending");
    },
    [fetchHomeMultidataAction.fulfilled](state,{payload}) {
      state.banners =payload.data.banner.list
      state.recommends =payload.data.recommend.list
      console.log("fulfilled");
    },
    [fetchHomeMultidataAction.rejected](state,action) {
      console.log("rejected");
    }
  }
```

#### extraReducer的另外一种写法

◼ **extraReducer还可以传入一个函数，函数接受一个builder参数。**

  我们可以向builder中添加case来监听异步操作的结果：

 链式调用

![在这里插入图片描述](https://img-blog.csdnimg.cn/da1caf1e8e914850b8693cecc354f890.png)

#### 案例

\-src

—store

\------- features

\------------- home.js

createAsyncThunk中完成异步操作

```js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";
import axios from "axios"

export const fetchHomeMultidataAction = createAsyncThunk("fetch/homemultidata", async () => {
  const res = await axios.get("http://123.207.32.32:8000/home/multidata")
  return res.data
})

const homeSlice = createSlice({
  name: "home",
  initialState: {
    banners: [],
    recommends: []
  },
  reducers: {
    changeBanners(state, { payload }) {
      state.banners = payload
    },
    changeRecommends(state, { payload }) {
      state.recommends = payload
    }
  },
  extraReducers:{
    [fetchHomeMultidataAction.pending](state,action) {
      console.log("pending");
    },
    [fetchHomeMultidataAction.fulfilled](state,{payload}) {
      state.banners =payload.data.banner.list
      state.recommends =payload.data.recommend.list
      console.log("fulfilled");
    },
    [fetchHomeMultidataAction.rejected](state,action) {
      console.log("rejected");
    }
  }
})

export const { changeBanners, changeRecommends } = homeSlice.actions

export default homeSlice.reducer 

```

\-src

—store

\-------index.js

把home中的reducer挂载到store中

```js
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from './features/counter'
// 不同的reducer管理
import homeReducer from './features/home'

const store = configureStore({
  reducer: {
    counter: counterReducer,
    home:homeReducer
  }
})

export default store
```

\-src

—pages

\-------Home.jsx

在页面中使用

```js
import React, { PureComponent } from "react";
import { connect } from "react-redux";
import { addNumber } from "../store/features/counter";
import { fetchHomeMultidataAction } from "../store/features/home";

export class Home extends PureComponent {
  async componentDidMount() {
    //dom渲染完成挂载阶段才能进行异步操作
    this.props.fetchHomeMultidata();
  }
  addNumber(num) {
    this.props.addNumber(num);
  }
  render() {
    //取出从从rudex里传入props中管理的状态
    const { counter, banners, recommends } = this.props;
    return (
      <div>
        <h2>Home Counter: {counter}</h2>
        <div>
          <button onClick={(e) => this.addNumber(5)}>+5</button>
          <button onClick={(e) => this.addNumber(8)}>+8</button>
        </div>
        <div className="banner">
          <h2>轮播图</h2>
          <ul>
            {banners.map((item) => {
              return <li key={item.title}> {item.title} </li>;
            })}
          </ul>
        </div>
        <div className="recommend">
          <h2>推荐数据展示</h2>
          <ul>
            {recommends.map((item) => {
              return <li key={item.title}> {item.title} </li>;
            })}
          </ul>
        </div>
      </div>
    );
  }
}

//把redux的state映射到Props
const mapStateToProps = (state) => {
  return {
    //counter的状态
    counter: state.counter.counter,
    //home的状态
    banners: state.home.banners,
    recommends: state.home.recommends,
  };
};

//把Dispatch映射到Props
const mapDispatchToProps = (dispatch) => {
  return {
    addNumber(num) {
      dispatch(addNumber(num));
    },
    fetchHomeMultidata() {
      dispatch(fetchHomeMultidataAction());
    },
  };
};

//connect高阶组件负责映射到组件Home
export default connect(mapStateToProps, mapDispatchToProps)(Home);

```