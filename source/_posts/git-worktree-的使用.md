---
title: Git worktree 的使用
author: Tomatoro
comments: true
tags: [Git]
top: 0
date: 2019-08-30 16:23:59
---

### 适用场景
> 在同一个 git 仓库中，有时候会遇到这样的情况, 你将你分支的代码开发好了,正在运行代码测试. 这时候你接到一个任务-->主分支上有一个bug需要立刻处理. 而你的代码测试已经跑了一大半了, 你不想结束这个测试, 但还必须去主分支上修改这个BUG, 这时候git stash的暂存明显是不能用的, 而重新clone一个新的项目下来有显得很麻烦. 这时候就是Git WorkTree 大展身手的时候了

git 从 2.6.0 的版本开始增加了新的指令，可以用来解决这个问题，就是：

```
git worktree
```

<!-- more -->

一个 git 仓库默认有一个 `worktree`，当需要在同一个仓库兼顾两个或者多个分支的开发时，可以为每一个分支新建一个 `worktree` 。他们彼此之间不会相互影响，其表现相当于在一个其他的目录重新 `git clone` 了一把这个 git 仓库。实际上与重建目录不同的是，他们彼此之间又有关联，任何一个 worktree 的提交都会无痛增加到其他的 `worktree` ，而不需要通过远程仓库做同步。一个分支的工作结束之后，删除那个分支对应的目录即可关闭这个 `worktree`。总之就是，看来还算完美的一个解决方案。

### 常用

##### `git worktree add <path> [<branch>]`：

增加一个新的 worktree ，并指定了其关联的目录是 `path` ，关联的分支是 `<branch>` 。后者是一个可选项，默认值是 `HEAD` 分支。如果 `<branch>` 已经被关联到了一个 worktree ，则这次 add 会被拒绝执行，可以通过增加 `-f | --force` 选项来强制执行。

同时，可以使用 `-b <new-branch>` 基于 `<branch>` 新建分支并使这个新分支关联到这个新的 worktree 。如果 `<new-branch>` 已经存在，则这次 add 会被拒绝，可以使用 `-B` 代替这里的 `-b` 来强制执行，则原来的 `<new-branch>` 的提交进度会被重置为和 `<branch>` 一样的位置。

##### `git worktree list` :

列出当前仓库已经存在的所有 `worktree` 的详细情况，包括每个 `worktree` 的关联目录，当前的提交点的哈希码和当前 checkout 到的关联分支。若没有关联分支，则是 `detached HEAD` 。

可以增加 `--porcelain` 选项，用来改变显示风格。即：使用 label 对应 value 的形式显示上面提到的内容。举例容易看出其中差别：

```
$ git worktree list
/path/to/bare-source            (bare)
/path/to/linked-worktree        abcd1234 [master]
/path/to/other-linked-worktree  1234abc  (detached HEAD)
```

```
$ git worktree list --porcelain
worktree /path/to/bare-source
bare
​
worktree /path/to/linked-worktree
HEAD abcd1234abcd1234abcd1234abcd1234abcd1234
branch refs/heads/master
​
worktree /path/to/other-linked-worktree
HEAD 1234abc1234abc1234abc1234abc1234abc1234a
detached
```

##### `prune` :

在删除 worktree 的关联目录之后，清除 worktree 的信息。从而使一个 worktree 完整的删除。

### 其他

##### `git worktree lock` :

git 会定期的自动清除掉已经没有关联目录的那些 worktree 的信息。当你把一个 worktree 的关联目录创建到了一个可移动设备或者一块不是永久挂载的硬盘里的时候，使用这个命令可以防止这个 worktree 的信息被移除。

##### `git worktree unlock` :

与上面的命令是一对。作用是解除锁定。

### 总结

看起来只是把 git 的文档大概翻译了一下，写一遍加深理解吧。也是刚开始使用的新命令，设计也是很稳重的，没有那么多花哨，没什么好的技巧可谈，但绝对是五星推荐的好用。

### 参考

[git worktree](https://link.jianshu.com?t=https%3A%2F%2Fgit-scm.com%2Fdocs%2Fgit-worktree)
