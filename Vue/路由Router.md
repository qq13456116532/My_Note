首先安装：
```node
npm i vue-router
```
然后需要在一个文件里面设置路由`router`，然后在`src`下创建一个 `router`的文件夹，然后创建`index.js`

此时一般都有views文件夹了，这里面放着页面文件，每个页面可能包含多个组件。

文件基本结构就是这样：
```js
import { createRouter,createWebHistory } from "vue-router";

import HomeView from "@/views/HomeView.vue";

const router = createRouter({
    history: createWebHistory(import.meta.env.BASE_URL),
    routes:[
        {
            path:'/',
            name:'home',
            component: HomeView,
        }
    ]
})

export default router;
```


然后要在`main.js`中导入router
```js
import router from '@/router/index'
```

正常的`main.js`中内容都是这样的
![](images/Pasted%20image%2020240824205808.png)

然后我们要把App拿出来，使用`use()`来使用路由
```js
const app = createApp(App)

app.use(router)

app.mount('#app')
```
这样就可以在各种文件里使用路由了


然后主页面的使用方法是：
```vue
<script setup>
import Navbar from '@/components/Navbar.vue';

import { RouterView } from 'vue-router';
</script>
<template>
  <Navbar></Navbar>
  <RouterView></RouterView>
</template>
```
`import { RouterView } from 'vue-router';` 这行代码从 `vue-router` 库中导入了 `RouterView` 组件，`RouterView` 是一个容器组件，用于显示当前路由匹配的视图内容。
`<RouterView></RouterView>` 用于展示根据当前路由动态变化的组件。当你在应用中导航时，`RouterView` 将根据当前的路由地址，显示相应的组件内容，如果地址在路由里面没有，那么会显示一个空白的页面。


然后在页面上跳转的方法是先导入`RouterLink`
```vue
<script setup>
import { RouterLink } from 'vue-router';
</script>
```
然后链接原本是`<a>`，现在改成：
```vue
<RouterLink to="/">Home</RouterLink>
```
原来的`href`也改成了`to`



# 404页面
然后路由里面必有的一个就是404页面，当其他路由不匹配时使用这个，就是写个404的相关页面，然后在路由里面配置：
```js
import NotFoundView from "@/views/NotFoundView.vue";
routes:[
   path:'/:catchAll(.*)',
   name:'not found',
   component: NotFoundView,
 },
    ]
```



# 传递参数请求
使用冒号
```js
 {
  path:'/jobs/:id',
  name:'job',
  component: JobView,
},
```
然后在被转移到的页面是这样使用这个参数
```js
import { useRoute } from 'vue-router';
const route = useRoute()
const jobId=route.params.id;
```



# 查看当前路径
有时可能需要判断当前路径是什么，以显示或不显示某些内容，常见于NavBar的图标，
```js
 import { useRoute } from 'vue-router';
 const isActiveLink = (routePath)=>{
  const route = useRoute();
  return route.path == routePath
}
```
这个代码就是判断当前路径是不是和传入路径相同。










