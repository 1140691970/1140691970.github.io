---
title: 在Vue.js中的延迟加载路由上命名Webpack块
date: 2020-10-12 10:55:12
categories: 前端
tags: [Vue, Vue路由]
---
# 在Vue.js中的延迟加载路由上命名Webpack块

## 转载来自：[参考文章](https://vuedose.tips/naming-webpack-chunks-on-lazy-loaded-routes-in-vuejs/)

在本技巧中，我想分享我在必须处理路线和延迟负载时通常的工作方式。
最有可能的是，您习惯于看到这样的Vue路线：

```js
import Vue from 'vue'
import VueRouter from 'vue-router'

import Home from '../views/Home.vue'
import About from '../views/About.vue'
import Login from '../views/Login.vue'

Vue.use(VueRouter)

const routes = [
  { path: '/', name: 'Home', component: Home },
  { path: '/about', name: 'About', component: About },
  { path: '/login', name: 'Login', component: Login }
]

const router = new VueRouter({
  routes
})

export default router
```

也许这样，通过异步加载路由并指定块名称：

```js
const routes = [
  {
    path: '/',
    name: 'Home',
    component: () => import(/* webpackChunkName: "Home" */ '../views/Home.vue')
  },
  {
    path: '/about',
    name: 'About',
    component: () => import(/* webpackChunkName: "About" */ '../views/About.vue')
  },
  {
    path: '/login',
    name: 'Login',
    component: () => import(/* webpackChunkName: "Login" */ '../views/Login.vue')
  }
]
```

这样真的可以，而且没有错。但是，在大多数情况下，我更喜欢使用其他系统，而不是使用“正常”近似值。
如您所见，有一些重复的模式。因为您是一位了不起的开发人员，所以可以将`array`与`route`选项一起使用，然后使用一个`map()`函数进行迭代，以避免执行重复的任务。
像这样：

```js
const routeOptions = [
  { path: '/', name: 'Home' },
  { path: '/about', name: 'About' },
  { path: '/login', name: 'Login' }
]

const routes = routeOptions.map(route => {
  return {
    ...route,
    component: () => import(`@/views/${route.name}.vue`)
  }
})

const router = new VueRouter({
  routes
})
```

🤩看起来不错，不是吗？
`component`通过`name`在`import`函数中将路径用作参数，我们减少了按键的使用。
但是..如果您要设置块名称怎么办？我不是科学家，但就我所掌握的知识而言，您无法在javascript中包含动态注释。因此，我们牺牲了注释（`webpackChunkName`），以减少编写代码的次数。由你决定。
玩笑。Webpack进行救援！从webpack开始2.6.0，占位符`index`和`request`被支持，这意味着您可以这样设置生成的块的名称：

```js
const routeOptions = [
  { path: '/', name: 'Home' },
  { path: '/about', name: 'About' },
  { path: '/login', name: 'Login' }
]

const routes = routeOptions.map(route => {
  return {
    ...route,
    component: () => import(/* webpackChunkName: "[request]" */ `../views/${route.name}.vue`)
  }
})

const router = new VueRouter({
  routes
})
```

nice非常好！现在，您将拥有所有具有命名块的动态加载路由。您可以通过npm run build在终端中运行来进行检查：
如您所见，在您的vue.js应用程序中实现它非常容易。现在轮到你了。router.js通过使用这个很棒的技巧开始改善您的文件。
