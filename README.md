# vue3-admin

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).


#### 升级vue-cli版本

```
npm update -g @vue/cli
```

#### 创建项目

vue create 项目名


#### eslint配合prettier格式化代码

安装prettier code formatter

根目录新建.prettierrc文件配置prettier规则(json格式)

```js
{
    "semi": false,
    "singleQuote": true,
    "trailingComma": "none"
}
```

保存自动格式化

1.设置

2.勾选Format On Save

若是保存时不生效, 右键选择 "...格式化文档",然后选择prettier

eslint 与prettier冲突时可以在.eslintrc中关闭该规则 例如

`'space-before-function-paren': 'off'`

修改后需要重启

#### Commitizen规范化提交代码

```
npm i -g commitizen
```

```
npm i cz-customizable -D
```

添加配置到package.json中

```javascript
"config": {
    "commitizen": {
      "path": "node_modules/cz-customizable"
    }
  },
```

根目录添加`.cz-config.js`文件

添加配置到该文件

```js
module.exports = {
  // 可选类型
  types: [
    { value: 'feat', name: 'feat:    新功能'},
    { value: 'fix', name: 'fix:    修复'},
    { value: 'docs', name: 'docs:    文档变更'},
    { value: 'style', name: 'style:    代码格式(不影响代码运行的变动)'},
    { value: 'refactor', name: 'refactor:    重构(既不是增加feature, 也不是修复bug)'},
    { value: 'perf', name: 'perf:    性能优化'},
    { value: 'test', name: 'test:    增加测试'},
    { value: 'chore', name: 'chore:    构建过程或辅助工具的变动'},
    { value: 'revert', name: 'revert:    回退'},
    { value: 'build', name: 'build:    打包'}
  ],
  // 消息步骤
  message: {
    type: '请选择提交类型',
    customScope: '请输入修改范围(可选)',
    subject: '请选择简要描述提交(必填)',
    body: '请输入详细描述(可选)',
    footer: '请输入要关闭的issue(可选)',
    confirmCommit: '确认使用以上信息提交? (y/n/e/h)'
  },
  // 跳过问题
  skipQuestions: ['body', 'footer'],
  subjectLimit: 72
}
```



#### 使用husky+commitlint检查提交描述

##### commitlint

`yarn add @commitlint/config-conventional@12.1.4 @commitlint/cli@12.1.4 -D`

创建commitlint.config.js

```js
module.exports =  {
  // 继承的规则
  extends: ['@commitlint/config-conventional'],
  // 定义规则
  rules: {
    // type 的类型定义: 表示git提交的type必须在以下类型范围之内
    'type-enum': [
      // 当前验证的错误级别
      2,
      // 什么情况下进行验证
      'always',
      // 泛型内容
      [
        'feat', // 新功能 feature
        'fix', // 修复 bug
        'docs', // 文档注释
        'style', // 代码格式
        'refactor', // 重构
        'perf', // 性能优化
        'test', //增加测试
        'chore', // 构建过程或辅助工具的变动
        'revert', // 回退
        'build' //打包
      ]
    ],
    // subject大小写不做校验
    'subject-case': [0]
  }
}
```

##### husky

`npm install husky@7.0.1 -D`

启动hooks, 生成.husky文件夹

`npx husky install`

在`package.json`中生成`prepare`指令(需要npm>7.0版本)
`npm set-script prepare "husky install"`

执行prepare指令
`npm run prepare`

添加`commitlint`的hook到husky中,并指令在`commit-msg`的`hooks`下执行`npx --no-install commitlint --edit "$1"`指令

`npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'`

通过pre-commit检测提交是代码规范

`npx husky add .husky/pre-commit "npx eslint --ext .js,.vue src"`



#### 使用lint-staged自动修复格式错误

##### 1.修改package.json或lint-staged.config.js配置

```js
"src/**/*.{js,vue}": [
    "eslint --fix",
    "git add"
  ]
```

##### 2.每次只会在`commit`之前,校验内容是否符合本地配置的eslint规则

​	1.符合规则: 则会提交成功

​	2.不符合规则: 自动执行`eslint --fix`尝试自动修复,如果修复成	功则把修复好的代码提交,如果失败,则需要自己修好后才能提交代码

##### 3.修改.`.husky/pre-commit`文件

```js
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged

```

##### 4.再次执行提交代码

##### 5.发现`暂存区中`不符合`ESlint`的内容,被自动修复
