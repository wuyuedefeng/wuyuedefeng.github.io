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
// 添加scoped用来生成唯一样式 指定内部样式只对当前组件生效, 不添加scoped样式将作用于全局
<style lang="scss" scoped> 
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
    Vue.$http = instance;
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

<<<<<<< HEAD
### 图片路径问题
[参考](https://github.com/zousandian/notes/issues/20)

配置alias
```
resolve: {
  extensions: ['', '.js', '.vue'],
  alias: {
    'assets': path.resolve(__dirname, '../src/assets')
  }
}
```
使用：
```
<img src="~asset/pic.png"></img>
```

如果配置了 vue html root
```
vue: {
  html: {
    root: path.resolve(__dirname, '../src/assets')
  }
}
```
则可以在任何子目录中直接写
```
<img src="/pic.png"></img>
```

动态渲染的图片

如果是有规律的重复出现的组件通常通过 v-for 来渲染，通过数组或对象来插入数据。
比如
```
const  data = [{
    title: 'title1',
    img: '../assets/pic01.png'
  }, {
    title: 'title2',
    img: '../assets/pic02.png'
}]

<my-compo v-for="item in data">
  <img :src="item.img"></img>
</my-compo>
```
这样的问题是 webpack 不会对 src 做任何操作，会原样输出导致 404 错误
```
<img src="../assets/pic01.png"></img>
```
这是因为渲染操作是在浏览器完成的。
解决办法是使用 require 引入图片让 webpack 在 build 的时候就知道要处理它们
```
const  data = [{
    title: 'title1',
    img: require('../assets/pic01.png')
  }, {
    title: 'title2',
    img: require('../assets/pic02.png')
}]
```
=======
### dev 和 pro 配置不同的变量

比如 api host
首先，我们分别找到下面的文件：
```
/config/dev.env.js
/config/prod.env.js
```

dev.en.js文件。代码如下：
```
var merge = require('webpack-merge')
var prodEnv = require('./prod.env')

module.exports = merge(prodEnv, {
  NODE_ENV: '"development"',
  API_ROOT: '"http://test.itrydo.com/api"'
})
```
prod.env.js文件:
```
module.exports = {
  NODE_ENV: '"production"',
  API_ROOT: '"http://www.baidu.com/api"'
}
```
在代码中调用设置好的参数:

```
// 配置API接口地址
var root = process.env.API_ROOT
```
在经过这样的配置之后，我们在运行
```
npm run dev
```
的时候，跑的就是测试接口。而我们运行
```
npm run build
```
打包项目的时候，打包的是服务器正式接口，我们就不用调来调去得了。

>>>>>>> 403e670eda1b20c855b845ea81b6c1689b4e28e4
---
### 问题列表

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