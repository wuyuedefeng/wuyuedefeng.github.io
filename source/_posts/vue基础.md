title: vue基础
author: wsen
tags:
  - vue
categories:
  - vue
date: 2017-03-26 16:49:00
---
### demo
```
var vm = new Vue({
	el: 'body',
    data: {
    	a: 1
    },
    // 计算属性
    computed: {
    	b: function(){ //默认是get
        	return this.a + 1;
        },
        c: {
        	get: function(){
            	return this.a + 1;
            }
            set: function(val){
            	this.a = val;
            }
        }
    },
    // 过滤器@1.x currency capitalize uppercase downcase debounce(ms) limitBy(count, index=0) filterBy(str) orderBy(1/-1) ...
    fileters: {
    
    },
    
    // @1.x transition
    // 动画过度 可通过类定义，也可使用transitions
    // <div class="animated" v-show="toggle" transition="bounce"></div>
    // 这里的class animated是animate.css做动画必须类！
    transitions: {
    	bounce: {
        	enterClass: 'zoomInLeft',
            leaveClass: 'zoomOutRight'
        }
    },
    
    // @2.x transition
    // .fade-enter{} // 初始状态
    // .fade-enter-active{} //变化成的状态->显示
    // .fade-leave{}
    // .fade-leave-active{} //变化成的状态->离开(消失)
    <transition name="fade" @before-enter="beforeEnter()" @enter="enter()" @after-enter="afterEnter" @before-leave="beforeLeave" @leave="leave" @after-leave="afterLeave">
    	<p></p>
    </transition>
    
    //2.x 结合animate.css使用
    <transition enter-active-class="zoomInLeft" leave-active-class="bounceOutRight">
    	<p class="animated" v-show="show"></p>
    </transition
    
    created: function(){}, // 创建之后
    beforeCompile: function(){}, // 编译之前 @1.x
    compiled: function(){}, // 变异之后 @1.x
    ready: function(){}, //节点插入到文档 @1.x
    
    beforeDestroy: function(){}, //销毁之前
    destroyed: function(){} // 销毁之后
})
vm.$el // 元素
vm.$data // data本身
vm.$mount // 手动挂载Vue程序
vm.options // 获取自定义属性对象
vm.$destroy // 销毁对象
vm.$log() // 查看当前数据状态

// 全局自定义过滤器
Vue.filter('toDouble', function(val){
	return val * 2;
});
// 双向过滤器 v-model 
Vue.filter('modelVal', {
	read: function(val){
    	return val
    },
    write: function(val){
    	return val;
    }
})

// 全局自定义指令
// <span v-red>abc</span>
Vue.directive('red', function(){
	//this.el 绑定该指令的元素(原生dom元素)
    this.el.style.background = 'red';
});
// 自定义元素指令 elementDirective

// 自定义键盘
// @keydown.ctrl = "show()"
// @keydown.mycustomenter = "show()"
Vue.directive('on').keyCodes.ctrl = 17 // @1.x
Vue.directive('on').keyCodes.mycustomenter = 13 //@1.x
// Vue.config.keyCodes.ctrl=17 //@2.x

// 监听数据变化
vm.$watch('a', function(){});
// vm.$watch('obj', function(){}, {deep: true});检测对象
```

### 自定义组件

#### 全局组件

方法1：

```
<body>
    <div id="box">
        <custom></custom>
    </div>
    <script>
    	// only: vue@1.x can use
        var Custom = Vue.extend({
        	data: function(){
            	return {
                	title: '我是自定义标题'
                }
            },
            template: '<h3>{{title}}</h3>'
        })
        Vue.component('custom', Custom);
        
        var vm = new Vue({
            el: '#box',
        })
    </script>
</body>
```
方法2： 
```
Vue.component('custom', {
	template: '<h3>我是自定义模板</h3>'
})
```

#### 局部组件
方法1： 
```
var Custom = Vue.extend({
    template: '<h3>我是自定义模板</h3>'
})

var vm = new Vue({
    el: '#box',
    components: {
    	custom: Custom
    }
})
```
方法2： 
```
new Vue({
    el: '#box',
    components: {
        custom: {
            template: '<h3>我是自定义模板</h3>'
        }
    }
})
```

#### 讲模板中的html提取到外部
```
<template id="custom">
    <h3>我是自定义模板</h3>
</template>
<script>
    new Vue({
        el: '#box',
        components: {
            custom: {
                template: '#custom'
            }
        }
    })
</script>
```

#### 动态组件

<component :is="组件名称"></component>
```
<body>
    <div id="box">
        <component :is="custom"></component>
    </div>
    <script>
        new Vue({
            el: '#box',
            data: {
                custom: 'a'  
            },
            components: {
                a: {
                    template: '<h1>我是a</h1>'
                },
                b: {
                    template: '<h1>我是b</h1>'
                }
            }
        })
    </script>
</body>
```
#### 调试工具

`dev-tools`

#### 父->子组件数据传递

`props`
> ['msg']

> {
	msg: String,
    num: Number
}

```
<body>
    <div id="box">
    	// @1.x中 .sync子组件更改，同步到父组件
        // @2.x中，不允许使用.sync了
        <custom :root.sync="root" :my-msg="msg"></custom>
    </div>
    <script>
        var vm = new Vue({
            el: '#box',
            data: {
            	root: 'root var'
                msg: 'my msg'
            },
            components: {
            	custom: {
				    props: ['root', 'myMsg'],
                	template: '<h3>{{root}} - {{myMsg}}</h3>'
                }
            }
        })
    </script>
</body>
```

#### 子->父组件数据传递

`$emit`: 发送

``


```
<body>
    <div id="box">
        <custom @child-msg="get"></custom>
    </div>
    <script>
        var vm = new Vue({
            el: '#box',
            data: {},
            methods: {
            	get: function(msg){
                	// msg: 'sub title'
                }
            },
            components: {
            	custom: {
	                template: '<h3>{{title}}</h3>'
                    data: function(){
                    	return {
                        	title: 'sub title'
                        }
                    },
                    methods: {
                    	send: function(){
                        	this.$emit('child-msg', this.title)
                        }
                    }
                }
            }
        })
    </script>
</body>
```

### slot 槽口

#### 匿名slot
```
<body>
    <div id="box">
        <custom>
            <p>I'm use slot</p>
        </custom>
    </div>
    <template id="custom">
        <h3>我是自定义模板</h3>
        <slot>为空的默认值</slot>
        <p>footer</p>
    </template>
    <script>
        new Vue({
            el: '#box',
            components: {
                custom: {
                    template: '#custom'
                }
            }
        })
    </script>
</body>
```
#### 命名slot
```
<custom>
	<p slot="slot1">I'm use slot</p>
    <p slot="slot2">I'm use slot</p>
</custom>
<template id="custom">
	<h3>我是自定义模板</h3>
	<slot name="slot1">为空的slot1默认值</slot>
    <slot name="slot2">为空的slot2默认值</slot>
	<p>footer</p>
</template>
```


### vue-router