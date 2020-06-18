## Vue 使用经验
1. 全局方法 `Vue.filter()` 注册一个自定义过滤器,必须放在Vue实例化前面，否则报：<font color='#FF9D78'>failed to resolve filter</font>

## 组件使用中的大小写问题
camelCase vs. kebab-case
**HTML 特性不区分大小写。当使用非字符串模版（如el）时，prop 的名字形式会从 camelCase 转为 kebab-case（短横线隔开）**
同时，使用 `$on(eventName)` 监听事件和 使用 `$emit(eventName)` 触发事件时，也需要注意大小写：**事件名不能使用驼峰 只能用小写 ，监听事件时也是一样 否则无法触发或者响应**
示例：
- Vue.component 组件中
```javascript 
//template中： @click="addTab(value1,value2)"
methods: {
        addTab: function (a,b){
            this.$emit('addtab',a,b);//触发父组件事件
        }
},
 props: {
        'menuArr': {
            type: Array,
            required: true
        }
    }
```
- 而在html页面中，props和监听子组件事件都要小写：menu-arr、addtab （而不是menuArr和addTab）
```html
<sidebar-menu :menu-arr="solimMenu" @addtab="addTabPage"></sidebar-menu>
```

### 字符串模板
字符串模板中不必注意大小写问题
- `<script type="text/x-template">`
- `.vue` 组件
- JavaScript内联模版字符串

## 列表渲染问题
当使用列表渲染（v-for）时，若数据可能出现变动，尤其是从中间某处删除某值（如splice一个值），需要加上key属性，否则panels变化之后，元素不能重新排序，导致显示出问题
>http://cn.vuejs.org/v2/guide/list.html#key

```javascript 
<template v-for="panel in panels">
     <tabpanel :tabpanel="panel" :key="panel.name">
           <iframeContent :content="panel"></iframeContent>
     </tabpanel>
</template>
```
### 遍历数组

>https://www.cnblogs.com/mmzuo-798/p/9435215.html

```javascript
<div v-for = "item in list" :key="index">{{ item }}</div>
<div v-for = "(item,index) in list" :key="index">{{ item }}-{{ index }}</div>
```

### 遍历对象 key-value

```javascript
<div v-for = "value in kvs" :key="index">{{ value }}</div>
<div v-for = "(value,key) in kvs" :key="index">{{ key }} - {{ value }}</div>
<div v-for = "(value,key,index) in kvs" :key="index">{{ value }}-{{ key }} -{{ index }}</div>
```

### 遍历对象数组

```javascript
<div v-for = "(item,index) in objArr" :key="index">{{ item.value }}-{{ item.key }} -{{ index }}</div>
```

## ajax中更新data

假设在某方法getajax中执行ajax更新数据：**必须在ajax之外定义变量指代 vue实例的 `this`，ajax中的`this`不是vue 的 this！**
```javascript 
getajax: function (url){
   var _this = this; //定义变量指代 vue 的 this
    $.get(url, function (json){
         _this.tdb = json.rows[0];// 可以更新数据
         this.tdb = json.rows[0];// 不能更新数据，this为ajax所有
         console.log(this);
     });
}
```
## radio、checkbox解决方案
### 一组radio，默认选择其中一个：
 name要相同，设置其中一个`:checked="checked"` checked 为bool类型
```
<div class="field" v-for="query in querytypes">
    <div class="ui radio checkbox">
        <input type="radio" :id="query.type" name="fq"
               :value="query.type" :checked="query.checked"
               tabindex="0">
        <label :for="query.type">{{query.des}}</label>
    </div>
</div>
```
### 一组checkbox，默认选中其中某个或某几个
设置v-model为checked
```
<div class="field" v-for="query in querytypes">
    <div class="ui checkbox">
        <input type="checkbox" :id="query.type" name="fq"
               :value="query.type" v-model="query.checked"
               tabindex="0">
        <label :for="query.type">{{query.des}}</label>
    </div>
</div>
```
全选/非全选，参考：http://fiddle.jshell.net/addbwdau/?utm_source=website&utm_medium=embed&utm_campaign=addbwdau
### vue-cli webpack模板项目
可使用命令行创建，或使用IDEA/WebStorm的项目【Project】创建
如果`npm install`失败
```shell 
npm ERR! phantomjs-prebuilt@2.1.14 install: `node install.js`
npm ERR! Exit status 1
```
试试：
`npm install phantomjs-prebuilt@2.1.14 --ignore-scripts`

为了加速 babel-loader，需要在 `test: /\.js$/`下面再加上 `exclude: /node_modules/`，同时在loader里面使用: `'babel-loader?cacheDirectory=true'`

## Vuex
![Vuex和组件之间的通讯关系](https://segmentfault.com/img/bVvcIk)
- 用户在组件中的输入操作触发 action 调用；
- Actions 通过分发 mutations 来修改 store 实例的状态；
- Store 实例的状态变化反过来又通过 getters 被组件获知。

每一个 Vuex 应用的核心就是 store（仓库），这里存放了应用中的大部分状态stage，操作和为维护状态的mutations，触发mutations的actions。
store实例尽管提供了改变状态的方法，它本身是不改变状态的。改变状态的源头来自组件和生命周期的钩子函数。 在组件中，使用计算属性来读取应用的状态，通过action——>mutation——>stage来改变应用的stage。

另外，并不是说应用的所有状态都应该放到vuex的stage中，stage主要放用于共享的，或者应用级别的状态，

>http://www.cnblogs.com/imgss/p/vuex.html