title: 使用vue-cli初始化vue项目
author: wsen
tags:
  - vue-cli
  - vue
categories:
  - vue
date: 2017-02-17 12:56:00
---
### init 
```
$ npm install -g vue-cli
$ vue init webpack demo
$ cd demo
$ npm install
```
`demo` 是这个示例项目的名字

### 现在看到目录结构如下
* `build` 目录是一些webpack的文件，配置参数什么的，一般不用动
* `src` 源码文件夹，基本上文件都应该放在这里。
* `static` 生成好的文件会放在这个目录下。
* `test` 测试文件夹，测试都写在这里
* `.babelrc` babel编译参数，vue开发需要babel编译
* `.editorconfig` 看名字是编辑器配置文件，不晓得是哪款编辑器，没有使用过。
* `.eslintrc.js` eslint配置文件，用以规范团队开发编码规范，大中型项目很有用
* `.gitignore` 用来过滤一些版本控制的文件，比如node_modules文件夹
* `index.html` 主页
* `package.json` 项目文件，记载着一些命令和依赖还有简要的项目描述信息
* `README.md` 介绍自己这个项目的，想怎么写怎么写。

### 添加支持 sass
> * 安装依赖包
```
npm install node-sass --save-dev
npm install sass-loader --save-dev
```
> * style中添加`lang="scss"`
```
// 添加scope用来生成唯一样式 指定内部样式只对当前组件生效, 不添加scope样式将作用于全局
<style lang="scss" scope> 
</style>
```

### 使用 [vue-router](http://router.vuejs.org/zh-cn/) 实现单页路由
```
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

const router =  new Router({
  routes: [
    {
      path: '/login',
      name: 'login',
      meta: { Auth: false },
      component: function (resolve) {
        require(['views/profiles/Login.vue'], resolve)
      },
      beforeEnter: (to, from, next) => {
        next();
      }
    },
    {
      path: '/',
      name: 'main',
      meta: { Auth: true },
      component: function (resolve) {
          require(['components/Main'], resolve)
      },
      children: [
        {
          path: '',
          name: 'index',
          component: function (resolve) {
            require(['views/Index'], resolve)
          }
        },
        // 用户管理
        {
          // 以 / 开头的嵌套路径会被当作根路径
          path: '/users', 
          name: 'users',
          component: function (resolve) {
            require(['views/users'], resolve)
          }
        },
        {
            path: '/users/:userId/edit',
            name: 'userEdit',
            component: function (resolve) {
                require(['views/users/userEdit'], resolve)
            }
        }
      ]
    },
    { path: '*', redirect: '/' },
  ]
});

router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.Auth) /* && is not login */) {
    next({
      path: '/login',
      query: {redirect: to.fullPath}
    })
  } else{
    next();
  }
})

export default router;
```
文件: [按需加载](https://github.com/vuejs/vue-router/blob/master/docs/zh-cn/lazy.md)

### 用 [vuex](https://vuex.vuejs.org/zh-cn) 管理我们的数据流
```
// 导入vuex
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

//导入modules
import users from './modules/users'
var store = new Vuex.Store({
  state: {
  },
  actions: {
  },
  mutations: {
  },
  modules: {
    users
  }
})
```

### 集成[axios](https://github.com/mzabriskie/axios)到项目中
创建http.js
```javascript
import axios from 'axios'
var store = null;

const instance = axios.create();

instance.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';

// Add a request interceptor
instance.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
}, function (error) {
    // Do something with request error
    return Promise.reject(error);
});

// Add a response interceptor
instance.interceptors.response.use(function (response) {
    // Do something with response data
    if(response.data.code != 10000){ 
        var error = new Error(response.data && response.data.msg);
        // alert(error);
        if(response.data.code == '50007' || response.data.code == '50004'){
            // store && store.commit('logout');
        }
        throw error;
    }
    return response;
}, function (error) {
    // Do something with response error
    // alert(error);
    return Promise.reject(error);
});

instance.install = function (Vue, options) {
    if(options.store) {
        store = options.store
    }
    Vue.prototype.$http = instance;
};

export default instance;
```
usage: 

eg: in main.js
```
import $http from './api/http'

Vue.use($http, {store});
```
then you can use in components
```
this.$http.get() //equal call axios instanse get 
```
or in vuex
```
Vue.$http.get()
```
细节，编写自定义插件 请自行参考[官方文档](https://cn.vuejs.org/v2/guide/plugins.html)

---
### main.js
```
import Vue from 'vue'
import App from './App'
import router from './router'
import store from './vuex/store'
import $http from './http'

Vue.use($http, {store});

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  store,
  template: '<App/>',
  components: { App }
})
```

---
#### 问题列表

* vue-cli build后 Webfonts字体路径引用出错的问题
 找到 `webpack.base.conf.js`替换
 
 ```
  {
      test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
      loader: 'url-loader',
      query: {
          limit: 10000,
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
      }
  }
 ```
 替换为
 ```
  {
      test: /\.(woff|woff2|eot|ttf|svg)(\?.*$|$)/,
      loader: 'url-loader?importLoaders=1&limit=100000'
  }
 ```
解决方法参考自 [github](https://github.com/vuejs/vue-cli/issues/56)