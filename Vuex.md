# Vuex

1. 概念
在Vue中实现集中式状态(数据)管理的一个Vue插件, 对vue应用中多个组件的共享状态进行集中式的管理(读/写)，也是一种组件间通信的方式，且适用于任意组件间通信。

2. 何时使用?  **多个组件需要共享数据时**

## 搭建vuex环境
1. 创建文件: src/store/index.js 引入Vue核心库
```js
import Vue from ' vue '
//引入Vuex
import Vuex from ' vuex '//应用Vuex插件
Vue. use(Vuex)
//准备actions对象一响应组件 中用户的动作
const actions = {
    actionName(context,value){  //context 上下文对象
        context.commit('ACTIONNAME',value)
    }
}
//准备mutations对象-修改state中 的数据
const mutations = {
    ACTIONNAME(state,value){
        state.stateName ...
    }
}
//准备state对象一保存具体的数据
const state = {
    stateName: ...
}
//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations ,
    state
})
```
2. 在main. js中创建vm时传入store配置项
```js
import store from ' . /store'
new Vue({
el:'#app',
render: h => h(App),
store
})
```
3. 在需要使用的vc组件中配置methods
```js
data(){
    return {
        n
    }
}
methods:{
    funcName(){
        this.$store.dispatch('actionName',this.n) //使用配置在vc中的store，调用dispatch方法
    }
}
```
4. 组件使用state需要配置computed,**如果数据很多的话使用mapState,降低代码重复度**
```js
//在template中直接使用插值语法如：{{statename}} 即可
computed:{
    statename(){
        return this.$store.state.stateName
    }
}
//使用前要从vuex部分引入 注意写法
import {mapStaet,mapGetter} from 'vuex'
//借助mapState从state中读取数据(对象写法)
...mapState({he:'sum',xuexiao:'school',xueke:'subject'})
//借助mapState从state中读取数据(数组写法)
...mapState(['sum','school','subject'])
//借助mapGetter从getters中读取数据也用以上写法，将State改写成Getter即可
//还有mapActions和mapMutations 写法大致一样 注意在template中调用methods时要写入参数
```
5. getters的使用
    1. 概念:当state中的数据需要经过加工后再使用时，可以使用getters加工。
    2. 在 store.js 中追加 getters 配置。
    3. 组件中读取数据:$store.getters.bigSum
```js
const getters = {
bigSum(state){
    return state.sum * 10
}
}
//创建并暴露store
export default new Vuex. Store({
    getters
})
```
## Vuex模块化+命名空间

1. 目的:让代码更好维护,让多种数据分类更加明确。
2. 修改 store.js
```js
const countAbout = {
    namespaced:true,//开启命名空间,
    mutations: {...},
    actions: { ... },
    getters:{
        bigSum(state){
            return state.sum * 10
        }
    }
}
const personAbout = {
    namespaced:true, //开启命名空间
    state:( ... ),
    mutations: {... },
    actions: {... }
}
const store = new Vuex . Store({
    modules: {
        countAbout ,
        personAbout
    }
})
```
3. 开启命名空间后，组件中读取state数据:
```js
//方式一:自己直接读取

this.$store.state. personAbout.list

//方式二:借助mapState读取:

...mapState(' countAbout' ,[' sum', 'school' ,'subject']),
```
4. 开启命名空间后，组件中读取getters数据:
```js
//方式一:自己直接读取

this.$store . getters['personAbout/firstPersonName']//方式二:借助mapGetters读取:

...mapGetters('countAbout' ,[' bigSum'])
```
5. 开启命名空间后，组件中调用dispatch
```js
//方式-:自己直接dispatch

this. $store .dispatch( ' personAbout/ addPersonWang' ,person)

//方式二:借助mapActions:

...mapActions(' countAbout ',{incrementOdd: 'jiaOdd'，incrementWait: 'jiaWait'})
```
6. 开启命名空间后，组件中调用commit
```js
//方式-:自己直接commit

this.$store . commit( ' personAbout/ADD_ PERSON' ，person)

//方式二:借助mapMutations:

...mapMutations(' countAbout', {increment: 'JIA' ,decrement: 'JIAN'})
```