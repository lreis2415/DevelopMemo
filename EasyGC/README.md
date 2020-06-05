# EGC 平台开发主要技术

https://github.com/lreis2415/cybersolim

项目相关文档资料：

- 项目 **README** 和 **WIKI**
- 有道云协作
- https://github.com/lreis2415/DevelopMemo

## 工具知识

-   Markdown  

    >   推荐使用 Typora 编辑器

- Git
  - 管理软件 SourceTree/Github Desktop/TortosiseGit
  - [git flow的使用](https://www.cnblogs.com/lcngu/p/5770288.html)
  - [git 常用命令](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

- 开发工具
    -   Java：IntelliJ IDEA(专业版/社区版)、MyEclipse、NetBeans、VS Code等
    -   前端：VS Code、 Atom、Sublime Text 或直接使用 Java IDE

- 开发类网站
    -   Google、百度
    -   各种开发框架、库的官方主页
    -   Github
    -   [stackoverflow](https://stackoverflow.com/)
    -   博客园、CSDN、开源中国
    
- Windows、Linux 基本命令行操作



## 代码执行基本流程

```sequence
用户界面 | Vue 组件-> Axios get/post: vue method 中方法
Axios get/post -> Controller: 发送请求+数据
Controller->Service: 调用实际业务逻辑
Service->(DAO)Mapper: 数据库请求
Service->调用CMD: 调用CMD命令行方法
调用CMD->Service: 返回处理结果
(DAO)Mapper->(XML)SQL: SQL查询请求
(XML)SQL->(DAO)Mapper: 返回查询结果
(DAO)Mapper->Service: 封装返回结果（domain 对象）
Service->Controller: 业务处理结果
Controller->Axios get/post: 返回响应
Axios get/post-> 用户界面 | Vue 组件: 显示响应数据
```



## 前端

### 1. 背景知识

-   HTML5+CSS3+JavaScript【掌握】Web开发根本  

    - [w3school](http://www.w3school.com.cn/index.html)
    - [Web 技术文档](https://developer.mozilla.org/zh-CN/docs/Web)

- SCSS 【掌握】 CSS的超集，在CSS的基础上增加了新的特性
    - [SASS用法指南](http://www.ruanyifeng.com/blog/2012/06/sass.html)
    - [sass语法](https://www.w3cplus.com/sassguide/syntax.html)

- [ES6](http://es6.ruanyifeng.com/)   【部分掌握】javascript语言的 ECMAScript 2015 版本
    - [深入浅出ES6](http://www.infoq.com/cn/es6-in-depth/)
    - [ES6新特性概览](http://www.cnblogs.com/Wayou/p/es6_new_features.html)
    - [ECMAScript 6 入门](http://es6.ruanyifeng.com/)

-   Webpack 【了解】前端资源加载/打包工具   [中文指南](https://doc.webpack-china.org/guides/get-started/) 


### 2. Nodejs 与 NPM 

搜索第三方库  https://www.npmjs.com/

[NodeJS的安装与基本使用](https://github.com/lreis2415/DevelopMemo/blob/master/Javascript/NodeJS%E7%9A%84%E5%AE%89%E8%A3%85%E4%B8%8E%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8.md)

### 3. Vue 

> https://cn.vuejs.org/v2/guide/

重点看  **基础**、**深入了解组件**、**路由**及**状态管理**

Vue 第三方库： https://github.com/vuejs/awesome-vue

Vue-cli 【了解】 Vue 开发脚手架

#### 1. 环境配置 

> 我们仍然是基于Vue 2，Vue 3的稍有不同

了解 webpack

自定义配置部分： `config/my.js`

使用自定义配置：

- `build/webpack.base.conf.js` 通用配置

- `build/webpack.dev.conf.js` 开发环境配置

- `build/webpack.base.prod.js ` 生产环境配置

### 4. Vuex 状态

https://vuex.vuejs.org/zh/

[Vuex学习](https://github.com/lreis2415/DevelopMemo/blob/master/Javascript/Vue/Vuex-%E5%AD%A6%E4%B9%A0.md)

 `src/store/index.js`  入口，组合各模块（为方便管理，将状态相关内容划分 modules）

1. `store/action-types`、`store/mutation-types` 中定义唯一的action 和 mutation 名称，便于后续使用
2. 在相应状态模块（如`store/modules/seims`）下定义state，mutations，actions： `state -> mutation -> action `
3. 变更状态顺序 `dispatch action -> mutation -> state change`



### 5. Vue Router 路由

https://router.vuejs.org/zh/

### 6. 界面设计

[iView](https://www.iviewui.com/docs/guide/install)  【掌握】 基于Vue的UI组件库

jsPanel

### 7. Axios

[Axios](https://github.com/mzabriskie/axios)  基于Promise 的HTTP 请求客户端，可同时在浏览器和node.js 中使用. 轻量级 Ajax 操作库

执行 http 请求：get/post 等

可以直接使用`this.$http.[get/post]`, 不必每个组件都引入 axios

参考：https://github.com/lreis2415/DevelopMemo/tree/master/Javascript/Axios

### 8. 地图：[OpenLayers](http://openlayers.org/)

### 9. Nginx 

 一款轻量级的 Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，其特点是占有内存少，并发能力强。

- [Nginx开发从入门到精通](http://tengine.taobao.org/book/)



## 后台

### 1. maven 及 nexus

- [maven配置](https://github.com/lreis2415/DevelopMemo/blob/master/Java/Maven%E9%85%8D%E7%BD%AE.md)

- maven 私有库配置（nexus）

### 2. Spring MVC

MVC

### 3. MyBatis

- 基本概念
- [代码生成](https://github.com/lreis2415/DevelopMemo/blob/master/Java/MyBatis%20%E7%94%9F%E6%88%90%E5%99%A8%E9%85%8D%E7%BD%AE.md)
- 生成代码更新

## Web Service
- [Apache CXF](http://cxf.apache.org) （Java Web Service开发, SOAP & RESTFul）
- [Apache ODE](http://ode.apache.org/) （Web Service 工作流）
- [52° North WPS](http://52north.org/communities/geoprocessing/wps/) (WPS)
- [GeoNetwork](https://geonetwork-opensource.org/) (CSW)

## 相关独立项目

- [egc-commons](https://github.com/lreis2415/egc-commons)  公共基础功能
- [egc-webservice](https://github.com/lreis2415/egc-webservices)  基于 Java 的 web service
- ~~easy-gis~~

