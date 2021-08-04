---
title: Notion绑定域名-你的笔记就是你的博客!
author: Tomatoro
comments: true
tags:
  - Blog
  - Notion
top: 0
abbrlink: c60ac904
date: 2019-11-06 16:04:28
---

为Notion公共页面提供自定义域名可能是最受要求的功能之一，而且目前看起来还不支持这样做（可以理解）但是，这里有一个使用Cloudflare Workers的解决方案。

## 第一步: 将你的域名服务器代理至Cloudflare

Cloudflare需要控制您的DNS，因此请按照本指南将名称服务器切换到它们。不用担心，您的DNS设置将保持不变。

[Changing your domain nameservers to Cloudflare](https://support.cloudflare.com/hc/en-us/articles/205195708-Changing-your-domain-nameservers-to-Cloudflare)

<!-- more -->

这一步很重要,大致可以理解为以下步骤:

1. 将你原来的nameservers更改为Cloudflare提供给你的nameserver

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odl22lx1j30q40e8jxk.jpg)

2. 比如我的域名是在阿里云的, 进行如下操作即可(修改后需要过一段时间等Cloudflare发邮箱给你)

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odlue2awj326s0r8k9w.jpg)

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odlzc9ofj31nw0sw487.jpg)

3. 在域名解析里添加一条A的记录,IP随便填

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odm4hp7zj31tu0duwlq.jpg)

4. 当收到邮件后,你的Cloudflare的Overview页会变成这样,就说明更改nameservers成功了

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odnejjxlj316u0gsag0.jpg)

5. 在Cloudflare的DNS页里也添加一条A的记录,IP随便填但是要保证Proxy Status是通的

   ![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odnkkaw5j31li0smtlp.jpg)

## 第二步: 添加工程脚本

Big thanks to [Mayne](http://github.com/mayneyao) for writing this worker script. You can find the original in [this gist](https://gist.github.com/mayneyao/b9fefc9625b76f70488e5d8c2a99315d).

以下是为你代理域的代码，因此请执行以下操作：

1. Click on the ”Workers” tab
2. Click “Launch Editor”
3. On the left, click ”Add Script”
4. Name it `notion-worker`

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odnqlau3j31lg0s04cz.jpg)

Once you have followed those steps, copy this script into that new file. 
``` JS
    const MY_DOMAIN = "example.com"
    const START_PAGE = "https://www.notion.so/link/to/your/public/page"
    
    addEventListener('fetch', event => {
      event.respondWith(fetchAndApply(event.request))
    })
    
    const corsHeaders = {
      "Access-Control-Allow-Origin": "*",
      "Access-Control-Allow-Methods": "GET, HEAD, POST,PUT, OPTIONS",
      "Access-Control-Allow-Headers": "Content-Type",
    }
    
    function handleOptions(request) {
      if (request.headers.get("Origin") !== null &&
        request.headers.get("Access-Control-Request-Method") !== null &&
        request.headers.get("Access-Control-Request-Headers") !== null) {
        // Handle CORS pre-flight request.
        return new Response(null, {
          headers: corsHeaders
        })
      } else {
        // Handle standard OPTIONS request.
        return new Response(null, {
          headers: {
            "Allow": "GET, HEAD, POST, PUT, OPTIONS",
          }
        })
      }
    }
    
    async function fetchAndApply(request) {
      if (request.method === "OPTIONS") {
        return handleOptions(request)
      }
      let url = new URL(request.url)
      let response
      if (url.pathname.startsWith("/app") && url.pathname.endsWith("js")) {
        response = await fetch(`https://www.notion.so${url.pathname}`)
        let body = await response.text()
        try {
          response = new Response(body.replace(/www.notion.so/g, MY_DOMAIN).replace(/notion.so/g, MY_DOMAIN), response)
          // response = new Response(response.body, response)
          response.headers.set('Content-Type', "application/x-javascript")
          console.log("get rewrite app.js")
        } catch (err) {
          console.log(err)
        }
    
      } else if ((url.pathname.startsWith("/api"))) {
        response = await fetch(`https://www.notion.so${url.pathname}`, {
          body: request.body, // must match 'Content-Type' header
          headers: {
            'content-type': 'application/json;charset=UTF-8',
            'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.103 Safari/537.36'
          },
          method: "POST", // *GET, POST, PUT, DELETE, etc.
        })
        response = new Response(response.body, response)
        response.headers.set('Access-Control-Allow-Origin', "*")
      } else if (url.pathname === `/`) {
    		let pageUrlList = START_PAGE.split("/")
        let redrictUrl = `https://${MY_DOMAIN}/${pageUrlList[pageUrlList.length-1]}`
        return Response.redirect(redrictUrl, 301)
      } else {
        response = await fetch(`https://www.notion.so${url.pathname}${url.search}`, {
          body: request.body, // must match 'Content-Type' header
          headers: request.headers,
          method: request.method, // *GET, POST, PUT, DELETE, etc.
        })
      }
    
      return response
    }
```

现在，你已经添加了脚本，您需要更改顶部的两个const：

1.  `MY_DOMAIN` 表示你需要代理的域名-**你自己的域名** ( E.G. example.com) 
2.  `START_PAGE` 表示你代理的目标域名地址-**notion的地址**(E.G.https://www.notion.so/link/to/your/public/page)

保存你的脚本,然后返回上一层

## 第三步: 添加一个通配符路径才处理你的所有流量

![](https://tva1.sinaimg.cn/large/006y8mN6ly1g8odnxcesuj31cu0u0wup.jpg)

在这里添加你的域名和通配符,然后在你Worker这一栏选择你刚刚配置的脚本名就可以了

    example.com/*

到这里为止就大功告成了! 这时候你访问你自己的域名就可以看到notion的页面啦, 以后用notion写博客也可以使用自己的域名了, 可谓是相当酷炫了! 

💎最后,这里有我的示例[tomatoro.space](https://tomatoro.space)
