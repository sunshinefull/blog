### babel  
---  
#### 使用方法  
- 使用单体文件  
- 命令行cli  
- 构建工具(webpack,rollup)  
#### preset  
1.包括@babel/preset-env,@babel/preset-react,@babel/preset-flow,@babel/preset-minify,@babel/preset-typescript  
2.stage-x，这里面包含的都是当年最新规范的草案  
3.es201x, latest  
#### 执行顺序  
- Plugin 会运行在 Preset 之前。
- Plugin 会从前到后顺序执行。
- Preset 的顺序则 刚好相反(从后向前)。 
#### env  
如果不写任何配置项，env 等价于 latest，也等价于 es2015 + es2016 + es2017 三个相加(不包含 stage-x 中的插件)  
```
{
  "presets": [
    ["@babel/preset-env", {
      "useBuiltIns": "usage",//按需加载@babel/polyfill
      "corejs": 3//core-js@3
      "targets": {
        "browsers": ["last 2 versions", "safari >= 7"]
        //"node": "6.10"
      }
    }]
  ]
}
```
#### 常用npm  
- @babel/cli   
顾名思义，cli 就是命令行工具。安装了 babel-cli 就能够在命令行中使用 babel 命令来编译文件。
在开发 npm package 时经常会使用如下模式：
- 把 babel-cli 安装为 devDependencies
- 在 package.json 中添加 scripts (比如 prepublish)，使用 babel 命令编译文件
- npm publish

这样既可以使用较新规范的 JS 语法编写源码，同时又能支持旧版环境。因为项目可能不太大，用不到构建工具 (webpack 或者 rollup)，于是在发布之前用 babel-cli 进行处理。
- @babel/node  
babel-node 是 babel-cli 的一部分，它不需要单独安装。
它的作用是在 node 环境中，直接运行 es2015 的代码，而不需要额外进行转码。例如我们有一个 js 文件以 es2015 的语法进行编写(如使用了箭头函数)。我们可以直接使用 babel-node es2015.js 进行执行，而不用再进行转码了。  
babel-node = babel-polyfill + babel-register  
- @babel/register  
babel-register 模块改写 require 命令，为它加上一个钩子。此后，每当使用 require 加载 .js、.jsx、.es 和 .es6 后缀名的文件，就会先用 babel 进行转码。
使用时，必须首先加载 require('babel-register')。  
需要注意的是，babel-register 只会对 require 命令加载的文件转码，而 不会对当前文件转码。  
另外，由于它是实时转码，所以 只适合在开发环境使用。  
- @babel/polyfill  
babel默认只转换js语法，不对转换新的api,如Generator,Set,Promise  
@babel/polyfill内部集成（core-js,regenerator） 
缺点 1.打出来的包非常大，还会污染全局变量  
- @babel/plugin-transform-runtime  
避免生成代码重复的定义如async/await,改为重复引用(devDependencies)  
如果我们希望 @babel/plugin-transform-runtime 不仅仅处理帮助函数，同时也能加载 polyfill 的话，我们需要给 @babel/plugin-transform-runtime 增加配置信息。
首先新增依赖 @babel/runtime-corejs3  
```
{
    "presets":[
        ["@babel/preset-env",{
            "useBuiltIns":"usage",
            "corejs":3 //npm i core-js -D
        }]
    ],
    "plugins":[
        [
            "@babel/plugin-transform-runtime",{
                "corejs":3//不会污染全局环境  npm i @babel/runtime-corejs3 -D
            }
        ]
    ]
}

```
- @babel/runtime  
@babel/plugin-transform-runtime的方法集  
@babel/runtime内部集成  
1. core-js  
2. helpers
- @babel/core  
    Babel 的核心功能包含在 @babel/core 模块中  
#### npm包  
- dependencies  
@babel/polyfill, (core-js@3,@babel/runtime-corejs3)二选一, @babel/runtime
- devDependencies  
@babel/core, @babel/cli, @babel/plugin-transform-runtime，@babel/register, @babel/preset-env, @babel/preset-typescript, @babel/preset-react  
#### 配置文件  
- babel.config.json  
- .babelrc.json  
- package.json  "babel"字段
