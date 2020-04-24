### eslint  
---
#### 安装和使用  
- 安装  
```
npm i eslint -D
```  
- 生成一个配置文件  
```
./node_modules/.bin/eslint --init
```  
#### 常用规则值的说明  
- "off" or 0 - 关闭规则
- "warn" or 1 - 将规则视为一个警告（不会影响退出码）
- "error" or 2 - 将规则视为一个错误 (退出码为1)  
####  配置文件及优先级  
1. .eslintrc.js
2. .eslintrc.yaml
3. .eslintrc.yml
4. .eslintrc.json
5. package.json "eslintConfig"字段  
#### eslintrc.js配置  
```
{
  "extends": ["eslint:recommended", "plugin:prettier/recommended"], // eslint扩展规则
  // 指定解析器选项
  "parserOptions": {
    "ecmaVersion": 7,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  // 指定解析器
  "parser": "babel-eslint",// 解决ES6 improt会报错 1.esprima 2.babel-eslint 3.@typescript-eslint/parser 
  // 指定脚本的运行环境
  "env": { // eg如果不配置browser，window就会被eslint报undefined的错
    "es6": true,
    "browser": true,
    "node": true
  },
  "plugins": ["prettier", "import"],
  "rules": {
    "prettier/prettier": "error",//关闭eslint和prettier重复校验的冲突问题
    "class-methods-use-this": 0,
    "import/no-named-as-default": 0,
    "react/jsx-filename-extension": [
      "error",
      {
        "extensions": [".js", ".jsx"]
      }
    ]
  }
}
```  
#### 规则的启用与禁用  
```
/* eslint eqeqeq: "off", curly: "error" */与下面相同
/* eslint eqeqeq: 0, curly: 2 */
/* eslint-disable */ //临时禁止规则出现警告
/* eslint-disable no-alert, no-console */ 对指定的规则启用或禁用警告
```  
完整的优先级  
1. 行内配置
    1. /*eslint-disable*/ 和 /*eslint-enable*/
    2. /*global*/
    3. /*eslint*/
    4. /*eslint-env*/
2. 命令行选项（或 CLIEngine 等价物）：
    1. --global
    2. --rule
    3. --env
    4. -c、--config
3. 项目级配置：
    1. 与要检测的文件在同一目录下的 .eslintrc.* 或 package.json 文件
    2. 继续在父级目录寻找 .eslintrc 或 package.json文件，直到根目录（包括根目录）或直到发现一个有"root": true的配置。
4. 如果不是（1）到（3）中的任何一种情况，退回到 ~/.eslintrc 中自定义的默认配置。  
#### 配合webpack和 prettier一起使用  
1.安装依赖  
```
npm i eslint eslint-loader prettier babel-eslint eslint-config-prettier eslint-plugin-prettier eslint-plugin-import
```  
2. prettier配置  
```
{
  "printWidth": 120, // 一行最大多少字符
  "tabWidth": 2, // tab占用的字符数
  "useTabs": false, // 是否使用tab代替空格
  "semi": true, // 是否每句后都加分号
  "singleQuote": true, // 是否使用单引号
  "jsxSingleQuote": false, // jsx是否使用单引号
  "trailingComma": "all", // 数组尾逗号。
  "bracketSpacing": false, // {foo: xx}还是{ foo: xx }
  "jsxBracketSameLine": false, //看官网
  "arrowParens": "always" //剪头函数参数是否使用（）
}
```
- 在eslintrc.json添加如下配置  
```
{
 "extends": ["eslint:recommended", "plugin:prettier/recommended"],
 "rules"{
     "prettier/prettier": "error",//关闭eslint和prettier重复校验的冲突问题
 }
}
```  
#### webpack配置  
```
module.exports = {
  // ...
  module: {
    rules: [
      {
        enforce: 'pre',
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'eslint-loader',
        options"{
            emitWarning:true //开发环境将所以规则设置为warning
        }
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader',
      },
    ],
  },
  // ...
};
```
 - husky钩子pre-commit配置  
 ```
 "husky": {
    "hooks": {
      "pre-commit": "lint-staged" // pre-commit，提交前的钩子
    }
  },
  "lint-staged": {
    // 此处可以配置文件夹和文件类型的范围
    "src/**/*.{jsx,tsx,ts,js,json,css,md}": [
      "prettier --write", // 先使用prettier进行格式化
      "eslint --fix", // 再使用eslint进行自动修复
      "git add" // 所有通过的话执行git
    ]
  }
 ```
 - 所需的插件  
**huskey** git commit git push前代码的扫描  
**eslint** 代码扫描  
**lint-staged**  就是每次只对当前修改后的文件进行扫描，即进行git add加入到stage区的文件进行扫描即可，完成对增量代码进行检查   
**prettier** 将格式化前的代码和格式化后的代码进行比对，如果发现不一样，prettier就会对其进行标记并按照指定的格式化规范进行修复   
- 安装husky，lint-staged    
npm i -D husky lint-staged  
#### 参考链接  
[eslint](https://eslint.org/)  
[prettier](https://prettier.io/docs/en/integrating-with-linters.html) 