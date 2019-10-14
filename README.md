# 项目介绍

基于 ts 的 vue 工程

## 41，搭建 vue 的开发环境

### 1，vue报错修复

在安装了 vue 后，在 `src\index.ts` 中简单编写了 vue 语法的代码，本地启动后，报如下错误：
```
vue.runtime.esm.js?6e6d:619 
[Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.

(found in <Root>)
```
原因是，当前使用的版本，是运行时版本，缺少编译器。

这是 vue 默认的版本，所以需要在 webpack 中指定，来引入一个完整的版本。

在 `build\webpack.base.config.js` 中，

设置一个别名 [alias](https://webpack.docschina.org/configuration/resolve/)，表示在引入 vue 时，将默认引入指定的文件，这里是 vue 的 es6 的版本 。
```
module.exports = {
  resolve: {
        alias: {
            'vue': 'vue/dist/vue.esm.js'
        }
    },
}
```

### 2，插件推荐

Vetur，vue专用插件，可以提升 vue 的开发效率。可以为 vue 文件提供代码高亮，自动补全等功能。

### 3，vue单文件

1. 声明文件
当编写了 `src\components\Hello.vue` 文件，并在 `src\index.ts` 中导入后，提示找不到该文件，

因为 ts 是无法识别 .vue 文件的，所以需要编写声明文件，`src\vue-shims.d.ts`，
- 将 .vue 文件声明为一个模块。
- 声明文件导出的类型是 Vue 构造器

这样引入的 .vue 文件，类型会被推断为 `VueConstructor<Vue>`

2. 其他依赖和配置添加

为了正确处理单文件组件，还需要一些依赖：
```
"devDependencies": {
    "css-loader": "^3.2.0",
    "vue-loader": "^15.7.1",
    "vue-template-compiler": "^2.6.10",
},
```

在 `build\webpack.base.config.js` 中，
```
// 使用 vue-loader 需要添加的插件。
const VueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
  resolve: {
    // 添加 .vue
    extensions: ['.js', '.ts', '.tsx', '.vue'],
  },
  module: {
    rules: [
      // 添加 vue-loader
      {
          test: /\.vue$/,
          loader: 'vue-loader'
      },
      {
          test: /\.tsx?$/,
          use: [{
              loader: 'ts-loader',
              // 添加配置，为了给 .vue 文件添加上.ts后缀，方便 ts-loader 的处理。
              options: {
                  appendTsSuffixTo: [/\.vue$/]
              }
          }],
          exclude: /node_modules/
      },
      // 添加 css-loader
      {
          test: /\.css$/,
          use: [
            'vue-style-loader',
            'css-loader'
          ]
      }
    ]
    },
}
```

总结，与一般 vue 开发的区别：
- 增加声明文件，让 ts 能够识别 vue 文件
- 增加了 ts-loader ，让其做语言转换和类型检查。

以上是手动创建 vue + ts 项目需要注意的问题，

> 如果是使用 vue-cli 创建 vue 项目，只需要在添加一个 typescript 的依赖即可，所有的配置工作 vue 都会自动完成。

--- 

## 43 组件发布，该项目使用

在 [ts-vue-component](https://github.com/crane0/ts-vue-component) 项目中，构建发布了一个组件 [vue-employee-query-crane0](https://www.npmjs.com/package/vue-employee-query-crane0)，

在这个项目测试使用一下，`src/index.ts`，

使用的就是 [ts-vue-component](https://github.com/crane0/ts-vue-component) 开发环境的代码