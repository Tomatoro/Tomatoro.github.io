---
title: Hexo博客添加Valine评论功能全过程
author: Tomatoro
comments: true
tags:
  - Blog
top: 0
abbrlink: 736d3b08
date: 2020-04-15 16:41:24
---



评论系统选择
------

Hexo 可用的评论系统有很多，如下：

来必力：[https://livere.com](https://livere.com/) （需要邮箱注册，加载慢，较卡顿）

畅言： [http://changyan.kuaizhan.com](http://changyan.kuaizhan.com/) （安装需要备案号）

Gitment： [https://github.com/imsun/gitment](https://github.com/imsun/gitment) （加载慢，有 Bug）

Valine: [https://github.com/xCss/Valine](https://github.com/xCss/Valine) (简约，实用，使用 Leancloud 作为线上数据库）

<!-- more -->

评论系统配置过程
--------

`next` 集成了 `leancloud` 。可以在`leancloud`进行账号注册。

### 1、注册 LeanCloud

注册地址 [https://leancloud.cn/](https://leancloud.cn/)

### 2、配置 LeanCloud

创建一个新的应用

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdujcpvt75j30mn082mxb.jpg)



随便取个名字，自己看着取吧

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdujctj3ysj30za0cwdgn.jpg)



应用创建完成，点开配置按钮

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdujcxa92qj30zx07kaae.jpg)



点击`设置` > `应用Key` 复制 App ID 和 App Key  

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdujd0mj33j30yl0edq43.jpg)



点击`设置` > `安全中心` 把自己博客网址添加到安全中心，保证数据的调用安全。  

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gdujd4a7sej30qn0n3wgb.jpg)



代码部分
----

#### One

![image-20200415160934042](https://tva1.sinaimg.cn/large/007S8ZIlly1gduintqm3gj30ng016glv.jpg)

```
# Valine
valine:
  enable: true
  el: 'vcomments'
  appId: '' # 你的appId
  appKey: '' # 你的appKey
  notify: true # 邮箱推送
  verify: true
  avatar: 'mp'
  pageSize: '10'
  placeholder: '请输入...'
```

#### Two

![image-20200415160845401](https://tva1.sinaimg.cn/large/007S8ZIlly1gduin17mcpj30iu012q39.jpg)

找到你的主题中放评论的位置加上valine这一行就可以

![image-20200415161007516](https://tva1.sinaimg.cn/large/007S8ZIlly1gduioeva58j30oo08ywik.jpg)



#### Three

![image-20200415161109883](https://tva1.sinaimg.cn/large/007S8ZIlly1gduiphphs6j30l401c74q.jpg)

这个文件里填上这些内容

```
<% if (theme.valine.enable) { %>
<div class="vcomments" id="<%- theme.valine.el %>"></div>
<%- js('https://unpkg.com/valine/dist/Valine.min.js') %>
<script>
	new Valine({
		el: '#<%- theme.valine.el %>',
		appId: '<%- theme.valine.appId %>',
		appKey: '<%- theme.valine.appKey %>',
		notify: '<%- theme.valine.notify %>',
		verify: '<%- theme.valine.verify %>',
		avatar: '<%- theme.valine.avatar %>',
		pageSize: '<%- theme.valine.pageSize %>',
		placeholder: '<%- theme.valine.placeholder %>'
	})
</script>
<% } %>
```



####Four

![image-20200415161311403](https://tva1.sinaimg.cn/large/007S8ZIlly1gduirlfwozj30kk01at92.jpg)

```
.vcomments
    margin-top: 3rem 
```



####Five

![image-20200415161208972](https://tva1.sinaimg.cn/large/007S8ZIlly1gduiqiiydjj30gw014wet.jpg)

引入上面这个css文件

![image-20200415161222135](https://tva1.sinaimg.cn/large/007S8ZIlly1gduiqqkl6tj30dc040jtg.jpg)

