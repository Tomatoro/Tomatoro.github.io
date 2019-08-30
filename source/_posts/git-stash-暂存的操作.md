---
title: Git stash 暂存的操作
author: Tomatoro
comments: true
tags: [Git]
top: 0
date: 2019-08-30 16:12:55
---

### 适用场景
> 我们在多人开发的时候,经常遇到开发某一个分支时,需要处理其他分支的事情. 这时就可以暂存手头的工作,切到其它分支,进行工作. 完事后再接回原来的分支并恢复之前的内容, 继续工作 Cool!

<!-- more -->

### 1. 暂存操作

```
git status  // 查看当前状态

git add . // 如果有修改,添加修改文件

git stash save '本次暂存的标识名字' // 暂存操作
```

### 2. 查看当前暂存的记录

```
git stash list // 查看记录
```

### 3. 恢复暂存的工作
> pop命令恢复,恢复后,暂存区域会删除当前的记录
```
git stash pop stash@{index}  // 恢复指定的暂存工作, 暂存记录保存在list内,需要通过list索引index取出恢复
```
> apply命令恢复,恢复后,暂存区域会保留当前的记录
```
git stash apply stash@{index} // 恢复指定的暂存工作, 暂存记录保存在list内,需要通过list索引index取出恢复
```
### 4. 删除暂存
```
git stash drop stash@{index} // 删除某个暂存, 暂存记录保存在list内,需要通过list索引index取出恢复

git stash clear // 删除全部暂存
```