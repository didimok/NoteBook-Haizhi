# Vue路由

1. 理解: 一个路由(route) 就是一组映射关系(key - value) ，多个路由需要路由器(router) 进行管理。
2. 前端路由: key是路径，value是组件。

## 基本使用

1. 安装vue-router, 命令: npm i vue-router

2. 应用插件: Vue . use(VueRouter)

3. 编写router配置项:
```js
//mian.js
//引入VueRouter

import VueRouter from ' vue -router ' //引入Luyou组件

import About from ' .. /components/About'

import Home from ' ../ components /Home'

//创建router实例对象，去管理一组一组的路由规则
const router = new VueRouter( {

routes:[
{
    path: ' /about ' ,component : About
},{
    path: ' /home ' ,component : Home
}
]

})
//暴露router

export default router
```
4. 实现切换(active-class可配置高亮样式)
```js
<router-link active-class="active" to="/about">About</router-link>
```
5. 指定展示位置
```js
<router-view></router-view>
```

## 几个注意点

1. 路由组件通常存放在pages文件夹，一般组件通常存放在components文件夹。

2. 通过切换,“隐藏"了的路由组件,默认是被销毁掉的，需要的时候再去挂载。

3. 每个组件都有自己的$route属性，里面存储着自己的路由信息。

4. 整个应用只有一个router, 可以通过组件的$router属性获取到。


## 路由的query参数

1. 传递参数  形式：在path中写入 ?参数&参数
```html
<!--跳转并携带query参数，to的字符串写法-->
<router-link :to=" /home/message/detail?id=66&title=你好" >ist </router-link>
<!--跳转并携带query参数，to的对象写法-->
<router-link
:to="{
    path: '/home/message/detail',
    query:{
    id:666,
    title:'你好'
}">
跳转 
</router-link>
```
2. 接收参数:
```js
//在组件中获取
$route.query.id
$route.query.title
```

## 路由的params参数

1. 配置路由，声明接收params参数  形式：在path中写入 /参数/参数
```js
{
path: '/home' ,
component :Home ,
children:[
path: 'news',
component : News
),
{
component :Message,
children:[
    {
        name: 'xiangqing' ,
        path: 'detail/:id/:title', //使用占位符声明接受params参数
        component :Detail
    }
]
}]
}
```

2. 传递参数
```html
<!-- 跳转并携带params参数， to的字符串写法-->
<router-link :to=" /home/message/detail/666/你好">跳转 </router-link>
<!--跳转并携带params参数，to的对象写法-->
<router-link
:to="{
name:' xiangqing ',
params:{
id:666 ,
title:'你好"
}
}">
跳转
</router-link>
```
**特别注意**:路由携带params参数时，若使用to的对象写法，则不能使用path配置项,必须使用name配置!

3. 接收参数:
```js
//在组件中
$route. params.id
$route . params. title
```

## 路由的props配置

作用:让路由组件更方便的收到参数
```js
{
name: 'xiangqing' ,
path: 'detail/:id',
component :Detail,

//第一 种写法: props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件
//props:{a:900}

//第二种写法: props值为布尔值，布尔值为true, 则把路由收到的所有params参数通过props传给Detail组件
//props:true

//第三种写法: props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
props(route){
return {
    id:route .query.id,
    title:route. query. title
}
}
}
```

## <router-link>的replace属性

1. 作用:控制路由跳转时操作浏览器历史记录的模式

2. 浏览器的历史记录有两种写入方式:分别为push和replace，push 是追加历史记录，replace 是替换当前记录，路由跳转时候默认为push

3. 如何开启replace模式:
```js
<router-link replace ...... >News</router-link>
```

## 编程式路由导航

1. 作用:不借助<router-link>实现路由跳转，让路由跳转更加灵活

2. 具体编码:
```js
//$router的两个API
this. $router. push({
    name: 'xiangqing',
    params:{
        id:xxx,
        title:xxx
    }
})

this. $router. replace({
    name :'xiangqing' ,
    params:{
        id:xxx,
        title:xxx
    }
})

this. $router. forward() //前进

this. $router.back() //后退

this. $router. go() //可前进也可后退
```

## 缓存路由组件

1. 作用:让不展示的路由组件保持挂载,不被销毁。

2. 具体编码:
```js
//多个组件  :include="[组件名,组件名...]"
<keep-alive include="News">
<router-view></router-view>
</keep-alive>

```

## 路由守卫

1. 作用:对路由进行权限控制

2. 分类:全局守卫、独享守卫、组件内守卫

3. 全局守卫:
```js
//全局前置守卫:初始化时执行、每次路由切换前执行
router.beforeEach((to,from,next)=>{
    console. log('beforeEach',to,from)
    if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
        if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
            next() //放行
        }else{
            alert( '暂无权限查看')
            // next({name: 'guanyu'})
        }
    }else{
        next() //放行
    })

//全局后置守卫:初始化时执行、每次路由切换后执行
router.afterEach((to, from)=>{
    console.log('afterEach' ,to,from)
    if(to.meta.title){
        document.title = to.meta.title //修改网页的title
    }else{
        document.title = 'vue_test'
    })
```

4. 独享守卫:
```js
//写在index的路由配置中
beforeEnter(to, from ,next){
    console.log( "beforeEnter" ,to, from)
    if(to .meta. isAuth){ //判断当前路由是否需要进行权限控制
        if(localStorage . getItem('school') === 'atguigu'){
            next()
        }else{
            alert( '暂无权限查看')
            // next((name: " guanyu "))
        }
    }else{
        next()
    }
}
```

5. 组件内守卫:
```js
//进入守卫，通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) {
},
//离开守卫:通过路由规则，离开该组件时被调用
beforeRouteLeave (to, from, next) {
}
```

## 路由器的两种工作模式

1. 对于一一个url来说， 什么是hash值? ——— #及其后面的内容就是hash值。

2. hash值不会包含在HTTP请求中，即: hash值不会带给服务器。

3. hash模式:

    1.  地址中永远带着#号,不美观。

    2. 若以后将地址通过第三方手机app分享,若app校验严格,则地址会被标记为不合法。

    3. 兼容性较好。

4. history模式:

    1. 地址干净,美观。

    2. 兼容性和hash模式相比略差。
    
    3. 应用部署上线时需要后端人员支持,解决刷新页面服务端404的问题。