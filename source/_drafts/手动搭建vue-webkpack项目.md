title: 手动搭建vue webkpack项目
author: wsen
date: 2017-02-22 17:59:47
tags:
---
### 初始化项目
创建 vue `[project-name]` 文件夹, 并进入执行
```
$ npm init
```
### 安装依赖包
```
$ npm install vue --save
$ npm install webpack webpack-dev-server vue-loader vue-html-loader css-loader vue-style-loader vue-hot-reload-api babel-loader babel-core babel-plugin-transform-runtime babel-preset-es2015 babel-runtime  html-webpack-plugin vue-template-compiler --save-dev
```