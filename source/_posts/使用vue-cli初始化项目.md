title: 使用vue-cli初始化项目
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


