# 安装hexo
```bash
npm install -g hexo-cli
```

# 开始使用

## 建站

[建站 | Hexo](https://hexo.io/zh-cn/docs/setup)

```bash
hexo init <folder>  # 注：folder为执行完此命令后项目所在文件夹名,此文件夹内不能有任何文件
```
新建完成后，指定文件夹的目录如下：

```
.
├── _config.yml
├── package.json
├── scaffolds
├── source
|   ├── _drafts
|   └── _posts
└── themes
```



### _config.yml
网站的 [配置](https://hexo.io/zh-cn/docs/configuration) 信息，可以在此配置大部分的参数。



### package.json

应用程序的信息。[EJS](https://ejs.co/), [Stylus](http://learnboost.github.io/stylus/) 和 [Markdown](http://daringfireball.net/projects/markdown/) renderer 已默认安装，可以自由移除。

```json
//package.json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": ""
  },
  "dependencies": {
    "hexo": "^3.8.0",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-renderer-marked": "^0.3.2",
    "hexo-server": "^0.3.3"
  }
}
```



### scaffolds

[模版](https://hexo.io/zh-cn/docs/writing#模版（Scaffold）) 文件夹。当新建文章时，Hexo 会根据 scaffold 来建立文件。

Hexo的模板是指在新建的文章文件中默认填充的内容。例如，如果您修改scaffold/post.md中的Front-matter内容，那么每次新建一篇文章时都会包含这个修改。



### source

资源文件夹是存放用户资源的地方。除 `_posts` 文件夹之外，开头命名为 `_` (下划线)的文件 / 文件夹和隐藏的文件将会被忽略。Markdown 和 HTML 文件会被解析并放到 `public` 文件夹，而其他文件会被拷贝过去。



### themes

[主题](https://hexo.io/zh-cn/docs/themes) 文件夹。Hexo 会根据主题来生成静态页面。



## 配置

[配置 | Hexo](https://hexo.io/zh-cn/docs/configuration)



## 命令

[指令 | Hexo](https://hexo.io/zh-cn/docs/commands)



# Fluid使用

[Hexo Fluid 用户手册 (fluid-dev.com)](https://hexo.fluid-dev.com/docs/)

安装主题

```bash	
npm install --save hexo-theme-fluid
```

然后在博客目录下创建 `_config.fluid.yml`，将主题的 [_config.yml (opens new window)](https://github.com/fluid-dev/hexo-theme-fluid/blob/master/_config.yml)内容复制过去。

以后修改fluid主题配置只需要修改此文件



# 本地运行

## 生成静态文件

```bash
$ hexo generate
```

| 选项              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| -d, --deploy      | 文件生成后立即部署网站                                       |
| -w, --watch       | 监视文件变动                                                 |
| -b, --bail        | 生成过程中如果发生任何未处理的异常则抛出异常                 |
| -f, --force       | 强制重新生成文件<br/>Hexo 引入了差分机制，如果 `public` 目录存在，那么 `hexo g` 只会重新生成改动的文件。<br/>使用该参数的效果接近 `hexo clean && hexo generate` |
| -c, --concurrency | 最大同时生成文件的数量，默认无限制                           |

该命令可以简写为

```bash
$ hexo g
```



## 启动服务器

```bash
$ hexo server
```

默认情况下，访问网址为：http://localhost:4000/

| 选项         | 描述                           |
| ------------ | ------------------------------ |
| -p, --port   | 重设端口                       |
| -s, --static | 只使用静态文件                 |
| -l, --log    | 启动日记记录，使用覆盖记录格式 |



## 清除缓存和静态文件

```bash
hexo clean
```

清除缓存文件 (`db.json`) 和已生成的静态文件 (`public`)。

如果修改过文件内容，需要通过如下命令清除已经生成的静态文件，重新生成。



# 部署到github pages

如果不希望将源代码上传至代码仓库，则使用方式一。

如果希望将代码上传至代码仓库，则使用方式二。

## 方式一

1.安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)。

```bash
$ npm install hexo-deployer-git --save
```



2.修改`_config.yml`中的参数

```yml
deploy:
  type: git
  repo: <repository url> #https://bitbucket.org/JohnSmith/johnsmith.bitbucket.io
  branch: [branch]
  message: [message]
```



3.生成站点文件并推送至远程仓库

```bash
$ hexo clean && hexo deploy
```

- 除非你使用令牌或 SSH 密钥认证，否则你会被提示提供目标仓库的用户名和密码。
- hexo-deployer-git 并不会存储你的用户名和密码. 请使用 [git-credential-cache](https://git-scm.com/docs/git-credential-cache) 来临时存储它们。



4.登录github，在库设置（Repository Settings）中将默认分支设置为`_config.yml`配置中的分支名称。



## 方式二

使用 [GitHub Actions](https://docs.github.com/zh/actions) 部署至 GitHub Pages

1. 建立名为 `<你的 GitHub 用户名>.github.io` 的储存库，若之前已将 Hexo 上传至其他储存库，将该储存库重命名即可。
2. 将 Hexo 文件夹中的文件 push 到储存库的默认分支，默认分支通常名为 `main`，旧一点的储存库可能名为 `master`。

- 将 `main` 分支 push 到 GitHub：

  ```
  $ git push -u origin main
  ```

- 默认情况下 `public/` 不会被上传(也不该被上传)，确保 `.gitignore` 文件中包含一行 `public/`。整体文件夹结构应该与 [范例储存库](https://github.com/hexojs/hexo-starter) 大致相似。

3. 使用 `node --version` 指令检查你电脑上的 Node.js 版本，并记下该版本 (例如：`v16.y.z`)

4. 在储存库中建立 `.github/workflows/pages.yml`，并填入以下内容 (将 `16` 替换为上个步骤中记下的版本)：

```
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

5. 当部署作业完成后，产生的页面会放在储存库中的 `gh-pages` 分支。

6. 在储存库中前往 `Settings > Pages > Source`，并将 branch 改为 `gh-pages`。

7. 前往 `https://<你的 GitHub 用户名>.github.io` 查看网站。

一键部署

1. 安装 [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)。
2. 在 `_config.yml` 中添加以下配置（如果配置已经存在，请将其替换为如下）:

```
deploy:
  type: git
  repo: https://github.com/<username>/<project>
  # example, https://github.com/hexojs/hexojs.github.io
  branch: gh-pages
```

1. 执行 `hexo clean && hexo deploy` 。
2. 浏览 `<GitHub 用户名>.github.io` 检查你的网站能否运作。

# 修改主题

https://hexo.io/themes/
### 1.安装新主题
下载新主题放到themes下（具体步骤根据主题官方的说明）

### 2.修改配置
修改项目根目录下的_config.yml，将theme指定的landscape修改为fluid(下载的主题名称)

### 3.根目录下执行命令
```bash
npm i hexo-generator-json-content --save
```

### 4.重新生成静态文件运行
```bash
hexo clean
hexo s -g
```
### 5.重新上传到代码仓库
```bash
hexo d
```

### 6.在代码仓库手动更新服务重新部署



# 写博客

## 方式一
执行如下命令
```bash
hexo new demo # demo为文件名
```
## 方式二
直接在_posts目录下手动创建markdown文件编写
```markdown
---
title: JVM
date: 2021-08-06 17:23:51
tags:
categories: 学习资料
mermaid: true
description: 测试
---

内容。。。
```

