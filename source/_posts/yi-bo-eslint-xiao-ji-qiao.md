---
title: '一波ESLint小技巧'
date: 2019-10-29 11:18:37
tags: [js,eslint]
published: true
hideInList: false
feature: /post-images/yi-bo-eslint-xiao-ji-qiao.jpg
---
最近借鉴总结了一波ESLint的小技巧，以记录自己的学习心得。现分享给大家，以供参考。如有不足，还请多多指教，谢谢大家。

<!-- more -->

参考文献：

- 1-VScode格式化ESlint-方法（最全最好用方法！）[Jump](https://www.jianshu.com/p/23a5d6194a4b)
- 我是如何在公司项目中使用ESLint来提升代码质量的[Jump](https://www.imooc.com/article/32222)
- vue-cli项目中 eslint语法检测在单文件中关闭检测的几种方式[Jump](https://blog.csdn.net/csl125/article/details/86591186)

### 关于ESLint

在团队开发中，出于代码规范的考虑，我们通常按照ESlint规范书写代码。加入ESLint有非常多的好处，比如说可以帮助我们避免一些非常低级的错误，一些格式上的问题导致我们在运行生产环境的时候出现一些不明所以的报错。还有就是在跟团队协作的时候，每个人都保持同一个风格进行代码书写，这样团队内部相互去看别人的代码的时候，就可以更容易的看懂。
但有些低级的格式问题经常被死板的ESLint卡住。。。所以我们应该有个自动格式化工具。

### Vue文件一键格式化

在vscode的文件-首选项-设置里，右侧配置里添加如下代码即可（代码有注释！），从此直接 Ctrl+S就能一键格式化了
```json
{
  // "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
  "vetur.validation.template": false,
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  "vetur.format.defaultFormatter.ts": "vscode-typescript",
  "window.zoomLevel": 2,
  "editor.tabSize": 2,
  "files.autoSave": "off",
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "html",
    {
      "language": "vue",
      "autoFix": true
    }
  ],
  "eslint.options": {
    "plugins": [
      "html"
    ]
  },
  "git.ignoreMissingGitWarning": true,
  // "git.autofetch": true
  "editor.wordWrap": "on",
  "search.location": "sidebar",
  "javascript.updateImportsOnFileMove.enabled": "always",
  "liveServer.settings.donotShowInfoMsg": true,
  "liveServer.settings.donotVerifyTags": true,
  "workbench.colorCustomizations": {
    "activityBarBadge.background": "#2979FF",
    "list.activeSelectionForeground": "#2979FF",
    "list.inactiveSelectionForeground": "#2979FF",
    "list.highlightForeground": "#2979FF",
    "scrollbarSlider.activeBackground": "#2979FF50",
    "editorSuggestWidget.highlightForeground": "#2979FF",
    "textLink.foreground": "#2979FF",
    "progressBar.background": "#2979FF",
    "pickerGroup.foreground": "#2979FF",
    "tab.activeBorder": "#2979FF",
    "notificationLink.foreground": "#2979FF",
    "editorWidget.resizeBorder": "#2979FF",
    "editorWidget.border": "#2979FF",
    "settings.modifiedItemIndicator": "#2979FF",
    "settings.headerForeground": "#2979FF",
    "panelTitle.activeBorder": "#2979FF",
    "breadcrumb.activeSelectionForeground": "#2979FF",
    "menu.selectionForeground": "#2979FF",
    "menubar.selectionForeground": "#2979FF",
    "editor.findMatchBorder": "#2979FF",
    "selection.background": "#2979FF40"
  },
  "materialTheme.accent": "Blue",
  "terminal.integrated.rendererType": "dom",
  // vscode默认启用了根据文件类型自动设置tabsize的选项
  "editor.detectIndentation": false,
  // 重新设定tabsize
  "editor.tabSize": 2,
  // #每次保存的时候自动格式化 
  "editor.formatOnSave": true,
  // #每次保存的时候将代码按eslint格式进行修复
  "eslint.autoFixOnSave": true,
  // 添加 vue 支持
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "vue",
      "autoFix": true
    }
  ],
  //  #让prettier使用eslint的代码格式进行校验 
  "prettier.eslintIntegration": true,
  //  #去掉代码结尾的分号 
  "prettier.semi": false,
  //  #使用带引号替代双引号 
  "prettier.singleQuote": true,
  //  #让函数(名)和后面的括号之间加个空格
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
  // #这个按用户自身习惯选择 
  "vetur.format.defaultFormatter.html": "js-beautify-html",
  // #让vue中的js按编辑器自带的ts格式进行格式化 
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  "vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
      "wrap_attributes": "force-aligned"
      // #vue组件中html代码格式化样式
    }
  },
  // 格式化stylus, 需安装Manta's Stylus Supremacy插件
  "stylusSupremacy.insertColons": false, // 是否插入冒号
  "stylusSupremacy.insertSemicolons": false, // 是否插入分好
  "stylusSupremacy.insertBraces": false, // 是否插入大括号
  "stylusSupremacy.insertNewLineAroundImports": false, // import之后是否换行
  "stylusSupremacy.insertNewLineAroundBlocks": false, // 两个选择器中是否换行
  "[markdown]": {
    "editor.defaultFormatter": "yzhang.markdown-all-in-one"
  },
  "editor.fontSize": 16
}
```

### 自动修复报错

在报很多错误之后，如果我们一条一条地去修复，就会变的非常的麻烦，相信刚接触ESLint的童鞋都深有体会。其实这些错误都可以让ESLint帮助我们自动地修复。

在命令行输入：
`npm run lint -- --fix`
你会发现，世界一下安静了许多，没有那么多飘红的报错，没有满屏的error和warning。

### 组件内关闭eslint检测

直接在代码文件中以注释的方式定义 需要注意的是，代码文件内以注释配置的规则会覆盖配置文件里的规则，即优先级要更高。

临时在一段代码中取消eslint检查，可以如下设置：
```javascript
/* eslint-disable */
 
// Disables all rules between comments 
alert(‘foo’);
 
/* eslint-enable */
```

临时在一段代码中取消个别规则的检查（如no-alert, no-console）：
```javascript
/* eslint-disable no-alert, no-console */
 
// Disables no-alert and no-console warnings between comments 
alert(‘foo’); 
console.log(‘bar’);
 
/* eslint-enable no-alert, no-console */
```

在整个文件中取消eslint检查：
```javascript
/* eslint-disable */
 
// Disables all rules for the rest of the file 
alert(‘foo’);
```

在整个文件中禁用某一项eslint规则的检查：
```javascript
/* eslint-disable no-alert */
 
// Disables no-alert for the rest of the file 
alert(‘foo’);
```

针对某一行禁用eslint检查：
```javascript
alert(‘foo’); // eslint-disable-line
 
// eslint-disable-next-line 
alert(‘foo’);
```

针对某一行的某一具体规则禁用eslint检查：
```javascript
alert(‘foo’); // eslint-disable-line no-alert
 
// eslint-disable-next-line no-alert 
alert(‘foo’);
```

针对某一行禁用多项具体规则的检查：
```javascript
alert(‘foo’); // eslint-disable-line no-alert, quotes, semi
 
// eslint-disable-next-line no-alert, quotes, semi 
alert(‘foo’); 
```

### 总结

……其实都是些取巧的小方法。
路漫漫其修远兮，与诸君共勉。
PMP，必胜！！！
***
作者：豆包君
链接：[https://juejin.im/post/5db6b901518825552e29a760](https://juejin.im/post/5db6b901518825552e29a760)
来源：掘金
著作权归作者所有。