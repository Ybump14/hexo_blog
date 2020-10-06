---
title: cypress
categories: notes
date: 2020-08-06 23:03:59
tags: cypress
---

[cypress文档地址](https://docs.cypress.io/zh-cn/guides/getting-started/installing-cypress.html#npm-install)

```
cd /your/project/path
```

```
npm init
```

```
npm install cypress --save-dev
```

将Cypress作为你项目的一个dev本地依赖安装到本地.

>打开Cypress
```
.\node_modules\.bin\cypress open
```

>添加npm脚本

每一次都输入完整路径的执行语句去执行Cypress是完全没有问题的, 只不过有更容易的和清晰的方式是添加Cypress命令到package.json文件的scripts区域.

```
{
  "scripts": {
    "cypress:open": "cypress open"
  }
}
```

现在，你可以在项目根目录下调用这个命令：
```
npm run cypress:open
```