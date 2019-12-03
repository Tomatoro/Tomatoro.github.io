---
title: Git在团队中的最佳实践
author: Tomatoro
comments: true
tags:
  - Git
top: 0
abbrlink: f33d987
date: 2019-10-23 11:00:14
---


![](https://tva1.sinaimg.cn/large/006y8mN6ly1g87ysjhs9oj30u014ek1b.jpg)

<!-- more -->

#### Git Flow常用的分支

- Production 分支

也就是我们经常使用的Master分支，这个分支最近发布到生产环境的代码，最近发布的Release， 这个分支只能从其他分支合并，不能在这个分支直接修改

- Develop 分支

这个分支是我们是我们的主开发分支，包含所有要发布到下一个Release的代码，这个主要合并与其他分支，比如Feature分支

- Feature 分支

这个分支主要是用来开发一个新的功能，一旦开发完成，我们合并回Develop分支进入下一个Release

- Release分支

当你需要一个发布一个新Release的时候，我们基于Develop分支创建一个Release分支，完成Release后，我们合并到Master和Develop分支

- Hotfix分支

当我们在Production发现新的Bug时候，我们需要创建一个Hotfix, 完成Hotfix后，我们合并回Master和Develop分支，所以Hotfix的改动会进入下一个Release

#### Git Flow如何工作

#### 初始分支

所有在Master分支上的Commit应该Tag

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yso01gdj30h2044glr.jpg)

#### Feature 分支

分支名 feature/*

Feature分支做完后，必须合并回Develop分支, 合并完分支后一般会删点这个Feature分支，但是我们也可以保留

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g87ysrtwikj30h207g3yz.jpg)

#### Release分支

分支名 release/*

Release分支基于Develop分支创建，打完Release分之后，我们可以在这个Release分支上测试，修改Bug等。同时，其它开发人员可以基于开发新的Feature (记住：一旦打了Release分支之后不要从Develop分支上合并新的改动到Release分支)

发布Release分支时，合并Release到Master和Develop， 同时在Master分支上打个Tag记住Release版本号，然后可以删除Release分支了。

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g87ysygdm3j30h208w0tc.jpg)

#### 维护分支 Hotfix

分支名 hotfix/*

hotfix分支基于Master分支创建，开发完后需要合并回Master和Develop分支，同时在Master上打一个tag

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g87yt25rc1j30h20akaat.jpg)

#### Git Flow代码示例

a. 创建develop分支

```bash
git branch develop
git push -u origin develop
```

b. 开始新Feature开发

```bash
git checkout -b some-feature develop
# Optionally, push branch to origin:
git push -u origin some-feature    

# 做一些改动    
git status
git add some-file
git commit
```

c. 完成Feature

```bash
# 合并feature到dev
git pull origin develop
git checkout develop
git merge --no-ff some-feature
git push origin develop

# 删除本地feature
git branch -d some-feature

# 删除远端feature
git push origin --delete some-feature
```

d. 开始Relase（test分支）

```bash
git checkout -b release-0.1.0 develop

# Optional: Bump version number, commit
# Prepare release, commit
```

e. 完成Release

```bash
# 合并到master
git checkout master
git merge --no-ff release-0.1.0
git push

#合并到dev
git checkout develop
git merge --no-ff release-0.1.0
git push

# 删除本地release
git branch -d release-0.1.0

# 删除远端release
git push origin --delete release-0.1.0   

# master打标记
git tag -a v0.1.0 master
git push --tags
```

f. 开始Hotfix（bugfix分支）

```bash
git checkout -b hotfix-0.1.1 master
```

g. 完成Hotfix

```bash
# 合并到master
git checkout master
git merge --no-ff hotfix-0.1.1
git push

# 合并到dev
git checkout develop
git merge --no-ff hotfix-0.1.1
git push

# 删除本地hotfix
git branch -d hotfix-0.1.1

# 为本次bug修改打标记
git tag -a v0.1.1 master
git push --tags
```