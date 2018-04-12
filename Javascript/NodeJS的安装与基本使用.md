# NodeJS的安装与基本使用

## 安装

https://nodejs.org/zh-cn/

下载推荐的 **LTS** 版（长期支持版）nodejs，并安装

不一定要安装到默认的 c 盘


## 修改npm模块安装目录
通过命令 `npm root -g` 查看可知默认的 node_modules 安装目录是 `${APPDATA}\npm`

如果需要修改，找到nodejs的安装目录下的配置文件，如`D:\Program Files\nodejs\node_modules\npm\npmrc`文件，修改为目标目录，如`prefix=D:\npm`，保存之后再次查看，已经修改为新的目录。

**注意**: 修改模块安装目录之后，需要相应地修改系统环境变量`PATH`，将`D:\npm`添加进去。否则会出现“xxx不是内部或外部命令，也不是可运行的程序或批处理文件

npm安装后会默认添加用户环境变量 `C:\Users\houzhiwei\AppData\Roaming\npm`



## 淘宝NPM镜像
>https://npm.taobao.org/

命令：`npm install -g cnpm --registry=https://registry.npm.taobao.org` 

之后可以使用`cnpm`来安装模块：`cnpm install [name]`

## 安装模块
`npm install html-loader --save-dev`  或 `npm i html-loader -D`

表示：安装名称为 html-loader 的模块，加入到 package.json 的 开发环境依赖 "devDependencies" 中

`npm install html-loader --save`或`npm i html-loader -S`  则是生产环境依赖 “dependencies”

## 卸载模块
`npm uninstall [<@scope>/]<pkg>[@<version>]... [-S|--save|-D|--save-dev|-O|--save-optional]`
## 更新
### 更新模块
- 查看模块`npm ls [[<@scope>/]<pkg> ...] `或 `npm` +`list, la, ll`之一
- 检查是否过时：`npm outdated [[<@scope>/]<pkg> ...]`
- 更新模块：`npm update [-g] [<pkg>...]`
>之后需要 `npm install -S / -D`

### 使用 npm-check-updates 监测并更新
>https://www.npmjs.com/package/npm-check-updates
>
>http://www.hostingadvice.com/how-to/update-npm-packages/

`npm install -g npm-check-updates`

切换到目标项目目录下，
- 检测  `ncu`
- 更新所有 `ncu -u`，并更新 `package.json`
- 更新模块(同时更新`package.json`) `ncu --upgrade [name]`  或  `ncu [name] -u`
```shell
-d, --dev                check only devDependencies
-f, --filter             include only package names matching the given string, 
                         comma-delimited list, or regex
-g, --global             check global packages instead of in the current project
-h, --help               output usage information
-m, --packageManager     npm or bower (default: npm)
-p, --prod               check only dependencies (not devDependencies)
-r, --registry           specify third-party NPM registry
-u, --upgrade            overwrite package file
-x, --reject             exclude packages matching the given string, comma-
                         delimited list, or regex
-V, --version            output the version number
-a, --upgradeAll         include even those dependencies whose latest
                         version satisfies the declared semver dependency
```

更新之后需要 `cnpm install`, 否则只是更新了`package.json`中的版本

### 使用 npm-upgrade 更新package.json
>https://github.com/th0r/npm-upgrade

`npm i -g npm-upgrade`

````shell
check [filter]    Check for outdated modules
ignore <command>  Manage ignored modules
npm-upgrade [filter] / npm-upgrade check [filter]

-p, --production   Check only "dependencies"
-d, --development  Check only "devDependencies"
-o, --optional     Check only "optionalDependencies"

npm-upgrade ignore <command>

Commands:
  add [module]       Add module to ignored list
  list                Show the list of ignored modules
  reset [modules...]  Reset ignored modules
````



### 更新 npm 
`npm update -g`
### 更新node.js
查看版本 `node -v`
安装工具包 `n`： `npm install -g n` 
>不支持Windows系统！

升级到最新稳定版：`n stable`
`n`后面可以是：`latest `、`stable`、`lts `（最新长期维护版Node）、`<version> `
