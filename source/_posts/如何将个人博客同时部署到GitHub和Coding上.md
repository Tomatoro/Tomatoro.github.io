---
title: 如何将个人博客同时部署到GitHub和Coding上
author: Tomatoro
comments: true
tags: Blog
top: 0
abbrlink: 3de92cb5
date: 2019-08-05 11:46:32
---

### 前言

> 之前我们把hexo托管在github，但是毕竟github是国外的，访问速度上还是有点慢，所以想也部署一套在国内的托管平台，下面给大家推荐一个国内代码托管的平台Coding。

### 可以学到什么

> 通过这篇文章，我们可以将我们的个人博客同时部署在国内和国外两个节点上。这样，如果是国内访问博客，会自动采用国内的节点，实现快速访问。如果是国外访问博客，便会采用国外的节点，以保证连接的可实现。

<!-- more --> 

## 正文



### _config.yml配置

* * *

想要同时部署到2个平台，就要修改博客根目录下面的`_config.yml`文件中的`deploy`如下
根据`Hexo官方文档`需要修改成下面的形式

```JS
deploy:
  type: git
  repo:
    github: <repository url>
    coding: <repository url>
    branch: [branch]

```

比如我这样
![_config.yml.png](https://tva1.sinaimg.cn/large/006y8mN6ly1g884gjg1q8j30uq07m0z5.jpg)


我这边提交采用的SSH密钥，这个方法有个好处，提交的时候不用输入用户名和密码。如果你习惯用http的方式，只要将地址改成相应的http地址即可。

### coding上创建一个新项目

* * *

这里只介绍coding上面如何创建项目，以及把本地hexo部署到coding上面，还不懂如何创建hexo的请看我之前的系类文章。首先我们创建一个项目，创建后进入项目的代码模块，获取到这个项目的ssh地址

![image](https://tva1.sinaimg.cn/large/006y8mN6ly1g884gm1ziwj31go0u0k9s.jpg)

### 同步本地hexo到coding上

* * *

把获取到了ssh配置在上面的`_config.yml`文件中的`deploy`下，本地打开 `id_rsa.pub` 文件，复制其中全部内容，填写到`SSH_RSA公钥`key下的一栏，公钥名称可以随意起名字。完成后点击“添加”，然后输入密码或动态码即可添加完成。

![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884unl6s2j31g80q64qp.jpg)


添加后，在`git bash`命令输入：

```bash
ssh -T git@git.coding.net
```

如果得到下面提示就表示公钥添加成功了：

```
Coding.net Tips : [Hello ! You've conected to Coding.net by SSH successfully! ]
```

最后使用部署命令就能把博客同步到coding上面：

```bash
hexo deploy -g
```

![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884gpwjeej31zy0u0e6o.jpg)

### pages服务方式部署

* * *

部署博客方式有两种，第一种就是pages服务的方式，也推荐这种方式，因为可以绑定域名，而第二种演示的方式必须升级会员才能绑定自定义域名。pages方式也很简单

分支选择master，因为前面配置的分支是master,因此开启之后，也需要是master。然后看起之后就可访问了。

![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884usvekgj31vo0u0tuz.jpg)

**注意**：

>1.  如果你的项目名称跟你`coding`的用户名一样，比如我的用户是叫`tengj`,博客项目名也叫`tengj` 那直接访问 `tengj.coding.me`就能访问博客，否则就要带上项目名：`tengj.coding.me/项目名` 才能访问推荐项目名跟用户名一样，这样就可以省略项目名了
> 2. 这里需要将自定义域名填上你自己的域名就可以了
> 3. `SSL/TLS安全证书`这里有个坑,就是如果你之前已经创建绑定过github的代码仓库,那么直接生成这个证书是生成不了的,他会显示失败的状态,需要过30分钟才可以再次申请。 所以到这一步的小伙伴，可以先把这一步空下来，继续网下看，会告诉该怎么操作才正确。



### 个人域名绑定

* * *

我是在阿里上买的tomatoro.cn的这个域名，现在要实现国内的走coding，海外的走github，只要配置2个CNAME就行。域名解析如下：
![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884gtkii2j310q0ne45d.jpg)
![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884gykdjbj310m0nowlf.jpg)
![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884wv3630j327i0ae10h.jpg)

> **注意**：
这里就说一下如果之前有设置过github的域名解析, 现在要再绑定一个coding的域名解析, 需要注意的点:
在coding去申请 `SSL/TLS安全证书`之前,需要将图中框起来的两个域名先暂停, 然后, 我们再去申请 `SSL/TLS安全证书`, 一般只需要等几秒就成功了, 然后再把这两个域名解析驱动就可以了. 一定要注意啊, 我就是因为这个等了半个小时才能接着申请的.

过几分钟后检测`tomatoro.cn`看到的解析是正确的，国内解析到Coding，国外解析到Github，如图：

![tomatoro.cn](https://tva1.sinaimg.cn/large/006y8mN6ly1g884hcq1f3j315k0u0kjl.jpg)

## 总结

* * *

到此为止，终于可以实现部署一次，`github`和`coding`两个同步都搞定了。访问速度也是唰唰唰的快，希望对还在搭建hexo独立博客的小伙伴有帮助。