---
date: 2024-12-30
tags:
  - Git
---
## 本地库和远程库的不同合并

如果本地库和远程库是两个项目，需要将两个项目合并，需要在 `git pull` 只会添加一些参数。需要添加 `--allow-unrelated-histories`，告诉git允许不相关历史合并。这通常用于：

- **初始化一个新的本地仓库，并将其与一个已有的远程仓库关联**。
- **将两个完全独立的仓库合并为一个**。

因为两个仓库有不同的起点，也就是两个仓库没有共同的 `commit`，此时无法拉取，如果还不能拉取，则先看一下是否产生冲突。

假如我们的远程库的源是master，分支是master，那么我们需要这么写：

```shell
$ git pull origin master --allow-unrelated-histories
$ git remote add origin https://github.com/yzj-again/testRepository.git
$ git push -u origin master
```