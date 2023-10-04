---
title: Fluid主题魔改
date: 2023-10-4 14:00:00
tags: Fluid
categories: Fluid
---

# Hexo Fluid主题魔改

参考了以下文章

https://asteri5m.gitee.io/archives/Fluid%E9%AD%94%E6%94%B9%E7%AC%94%E8%AE%B0.html

https://blog.kevinchu.top/2023/07/17/hexo-theme-fluid-modify

https://fomalhaut1998.github.io/posts/eec9786.html

# 添加自定义的css文件和js文件

在博客的source目录下新建一个js文件夹和一个css文件夹，把js和css文件分别放到这两个文件夹中。

将js和css文件添加到`_config.fluid.yml`配置文件中

```yml
# 指定自定义 .js 文件路径，支持列表；路径是相对 source 目录，如 /js/custom.js 对应存放目录 source/js/custom.js
# Specify the path of your custom js file, support list. The path is relative to the source directory, such as `/js/custom.js` corresponding to the directory `source/js/custom.js`
custom_js: 
    - /js/browser_title.js
    - /js/star.js
    - /js/dynamic_lines.js

# 指定自定义 .css 文件路径，用法和 custom_js 相同
# The usage is the same as custom_js
custom_css: 
    - /css/common.css
    - /css/cursor.css
    - /css/neonlamp.css
```



