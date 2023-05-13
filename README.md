# 安装hexo
```bash
npm install -g hexo-cli
```

# 初始化项目
```bash
hexo init blog  # 注：blog为执行完此命令后项目所在文件夹名
```
会生成node_models, scaffolds, source, themes文件夹；
生成.gitignore, _config.yml, package.json, yarn.lock

# 修改站点配置文件_config.yml
可参考Hexo官网提供文档进行修改：https://hexo.io/docs/configuration
注：deploy部署需要安装 hexo-deployer-git
```bash
npm install hexo-deployer-git --save
```

# 部署项目到github/gitee
执行如下命令生成静态文件（public文件夹），并提交到码云或github远程仓库上
```bash
hexo d
```

# 本地运行
执行命令
```bash
hexo s -g
```
默认访问端口4000

如果修改过文件内容，需要通过如下命令清除已经生成的静态文件，重新生成
```bash
hexo clean
```

# 修改主题
https://hexo.io/themes/
## 1.安装新主题
下载新主题放到themes下（具体步骤根据主题官方的说明）

## 2.修改配置
修改项目根目录下的_config.yml，将theme指定的landscape修改为fluid(下载的主题名称)

## 3.根目录下执行命令
```bash
npm i hexo-generator-json-content --save
```

## 4.重新生成静态文件运行
```bash
hexo clean
hexo s -g
```
## 5.重新上传到代码仓库
```bash
hexo d
```

## 6.在代码仓库手动更新服务重新部署

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

