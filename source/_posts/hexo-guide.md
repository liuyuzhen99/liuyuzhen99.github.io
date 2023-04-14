---
title: hexo_guide
date: 2023-04-14 09:35:37
layout: post
updated: 2023-04-14 09:52:30
tags: guide
---
# 博客搭建流程

## 安装 Node. js 和 git
**Node. js 版本限制**

hexo 版本|最低版本 (Node. js 版本)|最高版本 (Node. js 版本)|
:|:-:|:|
6.2+|12.13.0|latest|
6.0+|12.13.0|18.5.0|
5.0+|10.13.0|12.0.0|
4.1-4.2|8.10|10.0.0|
4.0|8.6|8.10.0|
3.3-3.9|6.9|8.0.0|
3.2-3.3|0.12|未知|
3.0-3.1|0.10 或 iojs|未知|
0.0.1-2.8|0.10|未知|
## 安装 hexo 
`npm install -g hexo-cli`

文件目录
```tree
.  
├── _config.yml  
├── package.json  
├── scaffolds  
├── source  
|   ├── _drafts  
|   └── _posts  
└── themes
```

**\_config. yml**
网站中的配置信息
*部署网站时把 url 改成自己的域名：https://liuyuzhen99.github.io/project*

**package. json**
应用程序的信息

**scaffolds**
模板文件夹。当您新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo 的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改 scaffold/post. md 中的 Front-matter 内容，那么每次新建一篇文章时都会包含这个修改。

**source**
资源文件夹是存放用户资源的地方。除 `_posts` 文件夹之外，开头命名为 `_` (下划线) 的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其他文件会被拷贝过去。

**themes**
[主题](https://hexo.io/zh-cn/docs/themes) 文件夹。Hexo 会根据主题来生成静态页面。

*如果部署失败，或者 404，可在 repo 根目录下添加名为. nojekyll 的空文件*

## GitHub Pages 部署

1. 建立 `liuyuzhen99. github. io`repo
2. 将 hexo 项目文件夹中的文件 push 到 repo 的 main 分支
```git
git add .
git commit -m "comment"
git push origin main
```

3. 使用 `node --version` 指令检查你电脑上的 Node. js 版本，并记下该版本 (例如：`v16.y.z`)
4. 在储存库中建立 `.github/workflows/pages.yml`，并填入以下内容 (将 `16` 替换为上个步骤中记下的版本)：

```yml
#.github/workflows/pages.yml

name: Pages  
  
on:  
  push:  
    branches:  
      - main # default branch  
  
jobs:  
  pages:  
    runs-on: ubuntu-latest  
    permissions:  
      contents: write  
    steps:  
      - uses: actions/checkout@v2  
      - name: Use Node.js 16.x  
        uses: actions/setup-node@v2  
        with:  
          node-version: "16"  
      - name: Cache NPM dependencies  
        uses: actions/cache@v2  
        with:  
          path: node_modules  
          key: ${{ runner.OS }}-npm-cache  
          restore-keys: |  
            ${{ runner.OS }}-npm-cache  
      - name: Install Dependencies  
        run: npm install  
      - name: Build  
        run: npm run build  
      - name: Deploy  
        uses: peaceiris/actions-gh-pages@v3  
        with:  
          github_token: ${{ secrets.GITHUB_TOKEN }}  
          publish_dir: ./public
```  

5. 当部署作业完成后，产生的页面会放在储存库中的 `gh-pages` 分支。
6. 在储存库中前往 `Settings > Pages > Source`，并将 branch 改为 `gh-pages`。
7. 安装`hexo-deployer-git`插件
8. 在`_config.yml`中添加配置
```yml
deploy:  
  type: git  
  repo: https://github.com/liuyuzhen99/liuyuzhen99.github.io  
  # example, https://github.com/hexojs/hexojs.github.io  
  branch: gh-pages
```
9. 执行`hexo clean && hexo deploy`
10. 浏览 [https://liuyuzhen99. github. io](https://liuyuzhen99. github. io)


