## 前言：给批阅的大佬们敬上
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf4586d2b132f8?w=268&h=227&f=png&s=32630)
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf4b5dc3db6460?w=255&h=255&f=png&s=54982)
## 一、项目效果
### 1.主页
![](https://user-gold-cdn.xitu.io/2019/9/2/16cef6c1b830d53d?w=375&h=667&f=png&s=232284)
### 2.城市选择
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf47d14dee464e?w=373&h=359&f=png&s=13507)
### 3.打包上线代码
![](https://user-gold-cdn.xitu.io/2019/9/2/16cef794b2a90a29?w=381&h=356&f=png&s=35913)
## 二、项目实战
### 1.项目简介
- Iconfont 的引入和使用
- 图片轮播组件的使用
- Axios 获取接口数据
- 组件间数据传递
- 字母表布局
- 使用FastClick处理移动端延迟
- 函数节流与防止性能优化
- LocalStorage 缓存选择城市
- Vuex 实现城市数据共享
- Keep-alive 性能优化
### 2.使用技术点
- Vue + Vue-router + Vue-cli + Axios + Vue-awesome-swiper + Stylus + Flex布局 + ES6 + Eslint + Webpack
### 3.相关npm依赖包
```
npm install vue-awesome-swiper@2.6.7 --save
npm install -g stylus
npm install fastclick --save
npm install axios -S
```
## 三、知识点回顾L
### 1.使用v-show实现更多按钮的展开收起，transform实现箭头旋转
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf46fc9fa35f6f?w=375&h=108&f=png&s=10540)

![](https://user-gold-cdn.xitu.io/2019/9/3/16cf4704b32810ce?w=375&h=168&f=png&s=14137)
```
.arrow1 {
    font-size: .24rem;
    transform: rotate(180deg);
}
```
#### [(推荐阅读一篇CSS文章)](https://juejin.im/post/5a0c184c51882531926e4294)
### 2.根据汽车Logo构建多页切换功能

![](https://user-gold-cdn.xitu.io/2019/9/3/16cf46ebec9ccbaa?w=376&h=199&f=png&s=32707)
```
computed: {
  pages () {
    const pages = []
    this.iconsList.forEach((item, index) => {
      const page = Math.floor(index / 8)
      if (!pages[page]) {
        pages[page] = []
      }
      pages[page].push(item)
    })
    return pages
  }
}
```
#### [(推荐阅读一篇算法文章)](https://juejin.im/post/5b72f0caf265da282809f3b5)
### 3.使用mock数据，开发环境代理
- 在config文件夹下设置index.js
- 在module.exports文件中dev的proxyTable设置
```
proxyTable: {
  '/api': {
    target: 'http://localhost:8080',
    pathRewrite: {
      '^/api': '/static/mock'
    }
  }
}
```
#### [(推荐阅读一篇webpack文章)](https://juejin.im/post/5aa3d2056fb9a028c36868aa)
### 4.由于只有大中国市级数据，城市选择采用这种展示更合理
- 上半部分 为 字母指引
- 下半部分 为 选择字母城市联动
- 城市数据多的，推荐better-scroll，并更换展示形式
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf47d14dee464e?w=373&h=359&f=png&s=13507)
```
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/pages/home/Home'
import City from '@/pages/city/City'
Vue.use(Router)

export default new Router({
  routes: [{
      path: '/',
      name: 'Home',
      component: Home
    }, {
      path: '/city',
      name: 'City',
      component: City
    }]
})
```
#### [(推荐阅读一篇vue组件库文章)](https://juejin.im/post/5af16a2cf265da0b8636353b)
### 5.使用 Vuex 实现数据共享
- 创建store文件夹，新建index.js，state 里放置全局公用数据city
```
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    city: '深圳'
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
    }
  }
})
```
- 在main.js的根实例下，将store传递进去。
```
import store from './store'  //引入 store

new Vue({
  el: '#app',
  router: router,
  store: store,  //传递进入根实例的 store
  components: { App },
  template: '<App/>'
})
```
- Vuex的commit方法(触发事件)和Vue-router的push方法(页面跳转)
```
methods: {
  handleCityClick (city) {
    this.$store.commit('changeCity', city)
    this.$router.push('/')
  }
}
```
- LocalStorage 实现城市缓存
```
export default new Vuex.Store({
  state: {
    city: localStorage.city || '深圳'
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
      localStorage.city = city
    }
  }
})
```
- 当用户禁用 localStorage，会导致浏览器报错，建议使用try catch进行优化
```
let defalutCity = '深圳'
try {
  if (localStorage.city) {
    defaultCity = localStorage.city
  }
} catch (e) {}

export default new Vuex.Store({
  state: {
    city: defaultCity
  },
  mutations: {
    changeCity (state, city) {
      state.city = city
      try {
        localStorage.city = city
      } catch (e) {}
    }
  }
})
```
#### [(推荐阅读一篇vuex文章)](https://juejin.im/post/58fffc52a22b9d0065b8db53)
### 6.Keep-alive结合activated生命周期钩子进行Ajax请求的优化
- 城市不变，汽车资讯不变
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf4972b6a57c45?w=375&h=488&f=png&s=96847)
### 7.优化
- 低版本浏览器白屏
```
npm install babel-polyfill --save
```
- 每次做路由切换时，让新显示的页面回到最顶部。
- 异步组件拆分，按需加载。
```
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
 
export default new Router({
  routes: [
    {
      path: '/',
      name: 'Home',
      component: () => import('@/pages/home/Home.vue')
    }, {
      path: '/city',
      name: 'City',
      component: () => import('@/pages/city/City.vue')
    }
  ],
  scrollBehavior (to, from, savedPosition) {
    return { x: 0, y: 0 }
  }
})
```
#### [(推荐阅读一篇性能优化文章)](https://juejin.im/post/5b960fcae51d450e9d645c5f)
### 8.打包上线
```
npm run build
```
## 三、为前端奋不顾身鸭！
![](https://user-gold-cdn.xitu.io/2019/9/3/16cf4aa6b6eb1815?w=1080&h=696&f=png&s=262232)
## 四、预告（下期中级篇更精彩，我们不见不散哦！）
## 五、结束语
#### 面试造航母，工作拧螺丝，我将会持续更新。
#### 这是我的微信公众号，欢迎关注！
![](https://user-gold-cdn.xitu.io/2019/8/10/16c7bd6bce08f303?w=291&h=276&f=png&s=37957)
