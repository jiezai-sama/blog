---
title: vue全局对象的挂载
date: 2022-11-21 18:33:05
tags: 
 - 前端
 - vue
categories: 
 - vue
cover: /image/cover1.jpg
---
## 1 在vue2中的应用
1. 挂载 -- 在main.js中
``Vue.prototype.$api = () => { ... }``
1. 使用
``this.$api.定义的方法``
---
## 2 在vue3中的应用
1. 挂载 -- 在main.js中
```JAVASCRIPT
import { createApp } from 'vue'
import App from './App.vue'
const app = createApp(App);
app.config.globalProperties.$api = () => { ... }
```
2. 使用<vue3不推荐使用全局挂载>
```JAVASCRIPT
//方法一
import { getCurrentInstance } from 'vue'
export default{
    set up() {
        const instance = getCurrentInstance()
        instance.appContext.config.globalProperties.$api
    }
}
```
```JAVASCRIPT
// 方法二
import { getCurrentInstance } from 'vue'
export default {
    set up() {
        const { proxy } = getCurrentInstance()
        proxy.$api
    }
}
```