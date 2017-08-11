---
title: Hexo 问题记录
date: 2017-08-04 16:18:55
tags: hexo issues
---

### Q: hexo deploy会发布整个项目到git master分支
A: 删除项目根目录下的 .git 文件夹

### Q: 删除.git文件夹后再hexo deploy报错 fatal: Not a git repository (or any of the parent directories): .git
A: 删除项目根目录下的 .deploy 文件夹