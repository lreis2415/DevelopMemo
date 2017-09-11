# vue-cli webpack

Vue webpack项目开始构建模板使用，关键内容摘要

>   中文文档 https://loulanyijian.github.io/vue-cli-doc-Chinese/
>
>   官方英文 http://vuejs-templates.github.io/webpack/
>
>   所有模板 https://github.com/vuejs-templates

## 0 安装和使用 vue-cli

```shell
$ npm install -g vue-cli # 全局安装vue-cli
$ vue init webpack my-project # 使用vue-cli初始化一个完整的webpack项目。
$ cd my-project # 进入目录
$ npm install # 安装依赖 (package.json)
$ npm run dev # 启动开发环境版本
```

使用Vue-cli命令行工具初始化基于模板的项目的命令语法：

`$ vue init <template-name> <project-name>`  

可用 `vue list `命令查看所有模板类型 

如果`npm install`失败

```shell
npm ERR! phantomjs-prebuilt@2.1.14 install: `node install.js`
npm ERR! Exit status 1
```

试试： 
`npm install phantomjs-prebuilt@2.1.14 --ignore-scripts`

## 1  项目结构

```shell
├── build/                      # webpack 编译任务配置文件: 开发环境与生产环境
│   └── ...
├── config/                     
│   ├── index.js                # 项目核心配置
│   └── ...
├── src/
│   ├── main.js                 # 程序入口文件
│   ├── App.vue                 # 程序入口vue组件
│   ├── components/             # 组件
│   │   └── ...
│   └── assets/                 # 模块资源 (会被webpack处理)
│       └── ...
├── static/                     # 纯静态资源 (直接拷贝到dist/static/里面)
├── test/
│   └── unit/                   # 单元测试
│   │   ├── specs/              # 测试规范
│   │   ├── index.js            # 测试入口文件
│   │   └── karma.conf.js       # 测试运行配置文件
│   └── e2e/                    # 端到端测试
│   │   ├── specs/              # 测试规范
│   │   ├── custom-assertions/  # 端到端测试自定义断言
│   │   ├── runner.js           # 运行测试的脚本
│   │   └── nightwatch.conf.js  # 运行测试的配置文件
├── .babelrc                    # babel 配置文件
├── .editorconfig               # 编辑配置文件
├── .eslintrc.js                # eslint 配置文件
├── index.html                  # index.html 入口模板文件
└── package.json                # 运行的脚本与相关依赖
```

### `build/`

包含实际为开发环境与生产环境的webpack配置文件。通常不需要关注这些文件，除非想自定义webpack的loader，这样的话，你应当先看看 `build/webpack.base.conf.js `这个文件。

### `config/index.js`

主要的配置文件，包含构建中最常用的一些配置。

### `src/`

应用程序源码文件。如何组织目录结构取决于开发者。如果使用Vuex/VueRouter，可以自行添加store/router目录。一般结构如下：

```shell
├── src/
|   ├── ...
└── store
|   ├── index.js          # where we assemble modules and export the store
|   ├── actions.js        # root actions
|   ├── mutations.js      # root mutations
|   └── modules
|   │   ├── cart.js       # cart module
|   │   └── products.js   # products module
└── router/           # 路由
│   └── routes.js
```

### `static/`

不想通过webpack处理的静态资源目录。这些目录中的资源会在webpack构建的时候，被直接复制到相同的目录中。

开发中使用的资源文件，在`src/assets`目录 。二者的区别：

-   `assets`中的文件会被webpack处理成模块依赖，这些资源可能会在构建过程中被内联/复制/重命名；而`static/`并非由Webpack来处理：它们被直接复制到最终目标目录。这些文件使用绝对路径，这个可通过`config/index.js`文件中的 `build.assetsPublicPath` 和 `build.assetsSubDirectory`来控制。

- 任何放置在`static/`的文件都使用绝对的URL `/static/[filename]`来引用。

    >   如果你修改`assetSubDirectory` 参数成 `assets`，然后这些URL需要变成`/assets/[filename]`。

>   http://vuejs-templates.github.io/webpack/static.html
>
>   https://loulanyijian.github.io/vue-cli-doc-Chinese/static.html

### `test/unit`

包含单元测试相关文件。

### `test/e2e`

包含端到端测试相关文件。

### `index.html`

应用中的 **模板** `index.html`。在开发环境中，webpack会生成相关资源，这些资源的url会自动插入到模板来渲染最终的HTML。

### `package.json`

NPM包元数据文件，包括所有的构建依赖与[构建命令](https://loulanyijian.github.io/vue-cli-doc-Chinese/commands.html)。

## 2 构建命令

### `npm run dev`

对应 package.json 中的NPM脚本命令

```json
"scripts": {
    "dev": "node build/dev-server.js",
    //...
}
```

### `npm run build`

对应 package.json 中的

```json
"scripts": {
    //...
    "build": "node build/build.js",
    //...
}
```

## 3 预处理器

这个模板已经预设大部分流行的 css 预处理器，包括 LESS, SASS, Stylus, 和 PostCSS。要使用一个预处理器的话 ，所有你需要做的就是安装相应的webpack loader。例如，使用SASS:

```shell
npm install sass-loader node-sass --save-dev
```

你需要安装`node-sass`，因为`saas-loader`需要这个依赖项

### 在组件中使用

安装成功后, 你可以通过修改`<style>`标签上的`lang`属性，在你的`*.vue`组件中使用预处理器。

```
<style lang="scss">
/* 写 SASS 代码! */
</style>
```

-   `lang="scss"` 对应CSS超集语法（用大括号和semicolones）。
-   `lang="sass"` 对应缩进语法。

### PostCSS

默认的情况下，在`*.vue`文件中的样式会被PostCSS处理，所以你不需要用一个特殊的loader来操作它。如果你想用它的话，你可以简单的在`build/webpack.base.conf.js`文件中添加PostCSS插件。

#### 使用配置文件

从11.0开始`vue-loader`支持自动加载[`postcss-loader`](https://github.com/postcss/postcss-loader#usage)支持的PostCss配置文件：

-   `postcss.config.js`
-   `.postcssrc`
-   `package.json`中的`postcss` 设置

使用配置文件允许您在由`postcss-loader`处理的常规CSS文件和`*.vue`文件内部CSS之间共享相同的配置，建议使用配置文件。

#### 内联选项

或者，可以使用`vue-loader`的`postcss`选项专门为`*.vue`文件指定postcss配置。 

```javascript
// webpack.config.js //for webpack 2.x
module.exports = {
  // other options...
  module: {
    // module.rules is the same as module.loaders in 1.x
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: {
          // ... // vue-loader options goes here
          postcss: [require('postcss-cssnext')()]
        }
      }
    ]
  }
}
```

除了提供一个插件数组外，该`postcss`选项还接受：

-   返回一个插件数组的函数;

- 包含要传递到PostCSS处理器选项的对象。这在使用依赖于自定义解析器/字符串的PostCSS项目时非常有用：

    ```javascript
    postcss: {
      plugins: [...], // list of plugins
      options: {
        parser: sugarss // use sugarss parser
      }
    }
    ```

>   http://vue-loader.vuejs.org/en/features/postcss.html

### 独立的 CSS 文件

为了确保一致的提取和处理，建议在根组件`App.vue`中引入全局的、独立的样式文件，例如：

```html
<!-- App.vue -->
<style src="./styles/global.less" lang="less"></style>
```

注意，你可能只需要为你自己编写的应用程序编写样式。在那些存在的样式库，如Bootstrap 或 Semantic UI，你可以在 `/static`文件夹中放置他们并且直接在`index.html`文件中引入。这样做会避免额外的构建时间，也更好的利用浏览器的缓存。(请参考[处理静态资源](https://loulanyijian.github.io/vue-cli-doc-Chinese/static.html))

## 4 环境变量

有时需要根据程序运行环境的不同而有不同的配置值。如

```javascript
// config/prod.env.js 生产环境
module.exports = {
  NODE_ENV: '"production"',  //字符串变量需要包成单引号和双引号 '"..."'
  DEBUG_MODE: false,
  API_KEY: '"..."' // 这是所有环境之间共享的
}

// config/dev.env.js  开发环境
module.exports = merge(prodEnv, {  // 继承自 prod.env .(通过merge)
  NODE_ENV: '"development"',
  DEBUG_MODE: true // 会覆盖prod.env中的debug_mode值
})

// config/test.env.js  测试环境
module.exports = merge(devEnv, {
  NODE_ENV: '"testing"'
})
```

### 用法

在代码中使用环境变量很简单。例如:

```json
Vue.config.debug = process.env.DEBUG_MODE
```
