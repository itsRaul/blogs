---
title: Vuex
date: 2018-10-09
tags: ["vuex"]
categories: vue
---
### Vuex的使用
<!-- more -->

### Vuex是什么
Vuex是vue的全局状态管理模式，为所有组件管理状态，当多组件共享状态时，应当使用vuex，数据更好进行管理。简单说就是无论父组件还是子组件或子子组件都可以直接从vuex获取状态，vuex提供统一状态管理，更好去管理数据。
#### Vuex图解
![image](/images/vuex.png)
1.通过提交mutations去改变state状态，从而state状态渲染到视图组件上
2.视图组件可以dispatch派发一个Action进行异步操作，异步操作在action里面，在action里面可以commit-mutation
3.视图组件可以派发action去提交Mutations，从而改变state

### State
1.state是vuex唯一的状态源，用一个对象包含了全部的应用层级状态。
```
state.js 

const state = {
    count:1
}
export default state;
```
2.如何在vue组件获取vuex状态
```
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```
通过在根实例注册store选项，注入到根组件的所有子组件，且子组件能通过 this.$store 访问到。通过计算属性返回某个状态，每当this.$store.state.count变化的时候，都会重新求取计算属性，计算属性有缓存。

3.mapState辅助函数
当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余，帮助我们生成计算属性，让你少按几次键
```
import { mapState } from 'vuex'
export defauilt {
    computed:{
        ...mapState(
            ['count']
        )
    }
}
```

### Getter
#### 简述
Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
```
getters.js

export const count = state => state.count
```
#### 访问方式
1.组件中使用它
```
computed: {
  doneTodosCount () {
    return this.$store.getters.count
  }
}
```
2.mapGetters辅助函数，个人比较喜欢当一种方式
使用对象展开运算符将 getter 混入 computed 对象中
```
import { mapGetters } from 'vuex'

export default {
  computed: { 
    ...mapGetters([
      'count',
      'anotherGetter',
    ])
  }
}
```
如果你想将一个 getter 属性另取一个名字，使用对象形式：
```
mapGetters({
  把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

### Mutation
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。个人比较常用的一种方式

#### Mutation配置
```
mutations-types.js

export const SET_MUTATION = 'export const SET_MUTATION'

```
使用常量替代 mutation 事件类型在各种 Flux 实现中是很常见的模式。这样可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个 app 包含的 mutation 一目了然：

```
mutations.js

import * as types from './mutations-types.js'

const mutations = {
  [types.SET_MUTATION](state,payload){

  }
}

export default mutations;
```
state参数可以获取vuex中所有数据源，payload是提交mutations传入的数据参数。
#### 提交mutation
1.mapMutations辅助函数
```
import { mapMutations } from 'vuex'

methods:{
  test(){
    this.mutation({
      amount: 10
    })
  },
  ...mapMutations({
    mutation:'types.SET_MUTATION'
  })
}
```
将 `this.mutation()` 映射为 `this.$store.commit('increment')`。`this.mutation()`提交mutation。
```
接收payload
mutations.js

import * as types from './mutations-types.js'

const mutations = {
  [types.SET_MUTATION](state,payload){
    state.count = payload.amount
  }
}

```

### Action
Action 提交的是 mutation，而不是直接变更状态。Action 可以包含任意异步操作。
#### 注册简单的action
```
import * as types from './mutations-types.js'

export const actionFn = function({commit,state},{amount}){
  commit(types.SET_MUTATION,amount)
}

```
#### 在组件中分发 Action
```
import { mapActions } from 'vuex'

methods:{
  test(){
    this.actionFn({amount:10})
  },
  ...mapActions([
    'actionFn'
  ])
}
```



