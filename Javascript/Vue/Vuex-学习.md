---
title: Vuex 学习
toc: true
description: 'Vuex学习记录 '
date: 2017-03-31 08:37:16
tags:
categories:
---

# Vuex : Vue的状态管理模式

>   摘自 https://vuex.vuejs.org/zh-cn/

## State

Vuex 使用 **单一状态树** :用一个对象包含全部的**应用层级**状态。它作为一个『唯一数据源([SSOT](https://en.wikipedia.org/wiki/Single_source_of_truth))』而存在。这意味着，每个应用将**仅仅包含一个 store 实例**。

### 在 Vue 组件中获得 Vuex 状态

在[计算属性](http://cn.vuejs.org/guide/computed.html)中返回某个状态:

```javascript
//Counter 组件
computed: {
    count () {
      return store.state.count  //依赖全局状态单例 store
    }
}
```

Vuex 通过 `store` 选项将状态从**根组件**『注入』到每一个子组件中

```javascript
Vue.use(Vuex); //必需调用
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
 // ...
```

 在子组件 Counter 中

```javascript
const Counter = {
  //...
  computed: {
    count () {
      return this.$store.state.count // 子组件能通过 this.$store 访问到
    }
  }
}
```

### `mapState` 辅助函数

当一个组件需要获取多个状态时候，将这些状态都声明为计算属性会有些重复和冗余。可使用 `mapState` 辅助函数帮助生成计算属性：

```javascript
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'
export default {
  // ...
computed: mapState({
    count: state => state.count,
    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',
    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
  //...
}
```

当映射的计算属性的名称与 state 的子节点**名称相同**时，我们也可以给 `mapState` 传一个**字符串数组**。

```javascript
computed: mapState([
  'count' // 映射 this.count 为 store.state.count
])
```

### 对象展开运算符

`mapState` 函数返回的是一个**对象**。我们如何将它与局部计算属性混合使用呢？使用[对象展开运算符](https://github.com/sebmarkbage/ecmascript-rest-spread)，可以极大地简化写法：

```javascript
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

## Getters

Vuex 允许在 store 中定义『getters』（可认为是 **store 的计算属性**），从而可以派生出新的状态（通过filter等等）。

Getters 接受 state 作为其第一个参数：

```javascript
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

Getters 会暴露为 `store.getters` 对象：

```javascript
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Getters 也可以接受其他 getters 作为第二个参数.

可在任何组件中使用它：

```javascript
computed: {
  doneTodos () {
    return this.$store.getters.doneTodos
  }
}
```

### `mapGetters` 辅助函数

`mapGetters` 辅助函数仅仅是将 store 中的 getters 映射到局部计算属性：

```javascript
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getters 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

如果想将一个 getter 属性**另取一个名字**，使用对象形式：

```javascript
mapGetters({
  // 映射 this.doneCount 为 store.getters.doneTodosCount
  doneCount: 'doneTodosCount'
})
```

# Mutations

更改 Vuex 的 store 中的状态的**唯一方法**是**提交 mutation**。

mutations 非常类似于**事件**：每个 mutation 都有一个字符串的 **事件类型 (type)** 和 一个 **回调函数 (handler)**。这个回调函数就是我们**实际进行状态更改的地方**，并且它会接受 **state 作为第一个参数**：

```javascript
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

**不能直接调用一个 mutation handler**。要唤醒一个 mutation handler，需**以 store.commit 方法调用相应的 type **：

```javascript
store.commit('increment')
```

### 提交载荷（Payload，额外参数）

可以向 `store.commit` 传入额外的参数，即 mutation 的 **载荷（payload）**：

```javascript
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}

store.commit('increment', 10)
```

在大多数情况下，载荷应该是一个**对象**.

### 对象风格的提交方式

直接使用包含 `type` 属性的对象：

```javascript
store.commit({
  type: 'increment',
  amount: 10
})
```

此时，整个对象都作为载荷传给 mutation 函数，因此 handler 保持不变：

```javascript
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

### Mutations 需遵守 Vue 的响应规则

Vuex 中的 mutation 也需要与使用 Vue 一样遵守一些注意事项：

1.  最好提前在 store 中初始化好所有所需属性。

2.  当需要在对象上添加**新属性**时，你应该

    -   使用 `Vue.set(obj, 'newProp', 123)`, 或者 -

    -   以**新对象替换老对象**。例如，利用 stage-3 的[对象展开运算符](https://github.com/sebmarkbage/ecmascript-rest-spread)我们可以这样写：

        ```javascript
        state.obj = { ...state.obj, newProp: 123 }
        ```

### 使用常量替代 Mutation 事件类型

使用常量替代 mutation 事件类型可以使 linter 之类的工具发挥作用，同时把这些常量放在单独的文件中可让代码合作者对整个 app 包含的 mutation 一目了然：

```javascript
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 使用 ES6 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})

```

### mutation 必须是同步函数

### 在组件中提交 Mutations

可以在组件中使用 `this.$store.commit('xxx')` 提交 mutation，或者使用 **`mapMutations`** 辅助函数将组件中的 methods 映射为 `store.commit` 调用（需要在根节点注入 `store`）。

```javascript
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    //字符串数组形式
    ...mapMutations([
      'increment' // 映射 this.increment() 为 this.$store.commit('increment')
    ]),
    //对象形式
    ...mapMutations({
      add: 'increment' // 映射 this.add() 为 this.$store.commit('increment')
    })
  }
}
```

# Actions

Action 类似于 mutation，不同在于：

-   Action **提交的是 mutation**，而不是直接变更状态。
-   Action **可包含任意异步操作**。

```javascript
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment') //提交 mutation increment
    }
  }
})
```

Action 函数接受一个**与 store 实例具有相同方法和属性**的 context 对象（ 不是 store 实例本身）。

可以调用 `context.commit` 提交一个 mutation，或者通过 `context.state` 和 `context.getters` 来获取 state 和 getters。

用 ES2015 的 [参数解构](https://github.com/lukehoban/es6features#destructuring) 来简化代码：

```javascript
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

### 分发 Action

Action 通过 `store.dispatch` 方法触发：

```javascript
store.dispatch('increment')
```

**mutation 必须同步执行**, 而在 action 内部可以执行**异步**操作。

Actions 支持同样的**载荷**方式和**对象**方式进行分发。

### 在组件中分发Action

可以在 组件中使用 `this.$store.dispatch('xxx')` 分发 action，或者使用 `mapActions` 辅助函数**将组件的 methods 映射为 `store.dispatch` 调用**（需要先在根节点注入 `store`）：

```javascript
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment' // 映射 this.increment() 为 this.$store.dispatch('increment')
    ]),
    ...mapActions({
      add: 'increment' // 映射 this.add() 为 this.$store.dispatch('increment')
    })
  }
}
```

# Modules

当应用变得很大时，store 对象会变得臃肿不堪。为此，Vuex 允许将 store 分割到**模块（module）**。每个模块拥有自己的 **state、mutation、action、getters**、甚至是**嵌套子模块**——从上至下进行类似的分割：

```javascript
const moduleA = {
  state: { ... },  //组件级别的状态
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}
const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  },
  state: { /*根级别的状态*/ }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

### 模块的局部状态

对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态**。对于getter，根节点状态会作为**第三个参数**。

```javascript
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

对于模块内部的 action，`context.state` 是局部状态，根节点的状态是 `context.rootState`.

### 命名空间

模块内部的 action、mutation和 getter 现在仍然注册在**全局命名空间**——这样保证了多个模块能够响应同一 mutation 或 action。即，在 Vuex 模块化中，state 是唯一会根据组合时模块的别名来添加层级的，后面的 getters、mutations 以及 actions 都是直接合并在 store 下。

可以通过添加前缀或后缀的方式隔离各模块，以避免名称冲突。

`export const DONE_COUNT = 'todos/DONE_COUNT'` 

### 模块动态注册

在 store 创建**之后**，你可以使用 `store.registerModule` 方法注册模块：

```javascript
store.registerModule('myModule', {
  // ...
})
```

模块的状态将是 `store.state.myModule`。

也可以使用 `store.unregisterModule(moduleName)` 动态地卸载模块。

>   不能使用此方法卸载静态模块（在创建 store 时声明的模块）。

## 表单处理

当在严格模式中使用 Vuex 时，在属于 Vuex 的 state 上使用 `v-model` 会比较棘手：`<input v-model="obj.message">` 

假设这里的 `obj` 是在计算属性中返回的一个属于 Vuex store 的对象，在用户输入时，`v-model` 会试图直接修改`obj.message`。在严格模式中，由于这个修改不是在 mutation 函数中执行的, 会抛出一个错误。

用『Vuex 的思维』去解决这个问题的方法是：给 `<input>` 中绑定 value，然后侦听 `input` 或者 `change` 事件，在事件回调中调用 action:

`<input :value="message" @input="updateMessage">`

```javascript
// ...
computed: {
  ...mapState({
    message: state => state.obj.message
  })
},
methods: {
  updateMessage (e) {
    this.$store.commit('updateMessage', e.target.value)
  }
}

// ...
mutations: {
  updateMessage (state, message) {
    state.obj.message = message
  }
}
```

### 双向绑定的计算属性

另一个方法是使用带有 setter 的双向绑定计算属性：

```javascript
<input v-model="message">
// ...
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}
```

---

定义状态state，为了获得派生状态，定义getters；定义改变状态的方法mutations，然后定义actions来调用mutation，通过dispatch触发actions。

`mapGetters` 将 store 中的 getters 映射到局部计算属性，`mapMutations` 将组件中的 methods 映射为 `store.commit` 调用，`mapActions` 将组件的 methods 映射为 `store.dispatch` 调用

---

## 目录结构

```
|——— store
	├── index.js          # 组装模块并导出 store 的地方
	├── actions.js        # 根级别的 action
	├── mutations.js      # 根级别的 mutation
	├── mutation-types.js # 使用常量替代 mutation 事件类型
	└── modules
        ├── m1.js         # 模块,内部包含模块的state、action、getter、mutation
        └── m2.js
```
