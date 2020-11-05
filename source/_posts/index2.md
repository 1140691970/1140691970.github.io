---
title: Vue 过滤器
date: 2020-07-29 15:27:12
categories: 前端
tags: Vue
---
# Vue 过滤器

## 什么是过滤器？
> 过滤器是对即将显示的数据做进一步的筛选处理，然后进行显示，值得注意的是过滤器并没有改变原来的数据，只是在原数据的基础上产生新的数据。

## 过滤器的创建
1. 全局过滤器
    * 首先新建一个单独的 filters.js 文件
        ```javascript
          const install = Vue => {
          	Vue.filter('filter_name', e => {
          		return e;
          	})
          }
          
          export default {
          	install
          }
        ```
    * 接着在 main.js 引入并使用
        ```javascript
          // 引入全局过滤器
          import filters from './common/filters.js'
          Vue.use(filters)
        ```
2. 局部过滤器
     * 直接在局部 filters 里创建
        ```javascript
           new Vue({       
              filters: {      
                 filter_name(e) { 
                   return e;
                 } 
              }    
           })
        ```

## 过滤器的使用
    // 例1
    {{ message | filter_name }}
    
    // 例2
    <div :text="message | filter_name"></div>
