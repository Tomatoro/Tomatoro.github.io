---
title: 如何在一台设备上同时配置github和gitlab的SSH
author: Tomatoro
comments: true
tags: Blog
top: 0
date: 2019-08-06 16:05:22
---

### 背景
> 在工作中，很有可能遇到这样的情况：公司用gitlab搭建了一个仓库，自己平常使用github来存储自己的代码。这样就造成在只设置了公司的gitlab SSH的时候，clone自己github仓库代码时，只能使用HTTPS的方式clone，很不方便。所以这篇文章会教会你同时在一台电脑上配置两个SSH，以方便自己在工作和个人空间上的快速切换，提高效率。

<!-- more-->
### 正文
首先，要找到位于用户下的.ssh文件，直接：
```
cd ~/.ssh
```
然后, 需要设置全局的name和email(这里注意,哪个常用设置哪个,我是公司常用,就设置的公司的)
```
git config --global user.name 'xxx'
git config --global user.email 'xxx@curefun.com'
```
接下来, 开始生成秘钥文件. 这里直接两个都生成一下
```
ssh-keygen -t rsa -C 'xxx@curefun.com' // GitLab
// Enter file in which to save the key (/Users/tomatoro/.ssh/id_rsa): id_rsa_gitlab
ssh-keygen -t rsa -C 'tomatoro@163.com' // GitHub
// Enter file in which to save the key (/Users/tomatoro/.ssh/id_rsa): id_rsa_github
```
进入到.ssh文件下,找到id_rsa_gitlab.pub和id_rsa_github.pub 将里面的内容全部复制粘贴到github 和 gitlab 的SSHKEY上
<img src="/img/ssh1.jpg" width="200px"/>
![image.png](/img/ssh2.jpg)
> 这里名字随便起, 然后记得.pub文件里内容全部复制就好了. 完了之后点保存. gitlab同理.

接下来就要将两个key在本地存储起来
打开agent
```
ssh-agent -s
ssh-add ~/.ssh/id_rsa_github // 输入生成秘钥时设置的密码
ssh-add ~/.ssh/id_rsa_gitlab // 输入生成秘钥时设置的密码
```
然后需要一个config文件来管理这两个key,以让git知道分配给谁
在.ssh目录下创建config文件
```
touch ~/.ssh/config
```
打开config编辑如下内容
```
Host github.com // 不动
    HostName ssh.github.com // 不动
    User tomatoro@163.com // 你自己的github邮箱
    PreferredAuthentications publickey // 不动
    IdentityFile ~/.ssh/id_rsa_github // 不动
    Port 443 
    // 如果ssh -T git@github.com的时候报 ssh: connect to host github.com port 22: Operation timed out就把Port这条加上吧,这个坑坑了我好久!!

Host 192.168.0.231 // 你们公司gitlab的ip地址
    HostName 192.168.0.231 //与Host保持一致
    User xxx@curefun.com // 你gitlab的邮箱
    IdentityFile ~/.ssh/id_rsa_gitlab // 不动
    Port 64222 // 你们公司gitlab的ip端口
```
好了,到了这一步,设置就基本全部完成了,接下来只需要跟远端的SSH同步一下就OK了
> 公司仓库下
```
ssh -T git@192.168.0.231
输入密码
git clone ssh://git@192.168.0.231:64222/MLE/skillCenter.git
```
> 个人仓库下
```
git init // --local需要在有git仓库的情况下才可以执行
git config —local user.name 'tomatoro'
git config —local user.email 'tomatoro@163.com'
ssh -T git@github.com
输入密码
git clone ssh://git@github.com:443/Tomatoro/TypeScript-study.git
```
至此,就全部结束了. 想想我在搞这个东西的时候遇到的坑,现在都觉得好恶心. 整整弄了一下午, 希望对后来者有些许帮助吧.
全部原创,欢迎转载!转载请注明出处.谢谢!

