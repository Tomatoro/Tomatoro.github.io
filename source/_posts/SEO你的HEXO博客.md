---
title: SEO你的HEXO博客
author: Tomatoro
comments: true
tags:
  - Blog
  - SEO
top: 0
keyword: 'SEO,HEXO,博客,搜索引擎,收录'
abbrlink: a6b6c05e
date: 2019-12-03 12:31:11
---

### STEP1      百度收录站点

登录[百度站长平台](http://zhanzhang.baidu.com/)，在用户中心 => 站点管理添加你的站点网址

<!-- more -->

配置完站点属性后，进入最后一步：验证网站。有三种方式：文件验证、HTML标签验证、CNAME验证.( 这里如果不会自行百度一下吧,网上内容有很多,这里就不在累述了. )

![HEXO%20SEO/Untitled.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf84kymlj30y50lydl0.jpg)

### STEP2      keywords 和 url地址栏优化

- 在根目录和themes目录下的两个_config.yml文件下找到permalink将其修改为下面这样:

``` bash
  # permalink: :year/:month/:day/:title/
  permalink: archives/:abbrlink.html
  permalink_defaults:
  abbrlink:
    alg: crc32  # 算法：crc16(default) and crc32
    rep: hex
```

- 安装相关的依赖

``` bash
      npm install hexo-abbrlink -S
```

- 执行hexo部署命名

``` bash
      hexo g
      hexo d
```

- 完成!

![HEXO%20SEO/Untitled%201.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf88nkb6j30wm0ncdvb.jpg)

### STEP3      配置百度 主动推送 自动推送 和sitemap

![HEXO%20SEO/Untitled%202.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf8ekxbwj30rq05ndgx.jpg)

- 安装相关依赖

``` bash
      npm install hexo-baidu-url-submit -S
      npm install hexo-generator-sitemap -S
      npm install hexo-generator-baidu-sitemap -S
```

- 主动推送和sitemap的配置
``` bash
      deploy:
      - type: baidu_url_submitter ## 这是新加的
        bucket: tomatoro.cn ## 你自己的域名
      
      sitemap:
        path: sitemap.xml
      baidusitemap:
        path: baidusitemap.xml

      baidu_url_submit:
        count: 100 ## 比如3，代表提交最新的三个链接
        host: tomatoro.cn ## 在百度站长平台中注册的域名
        token: --------- ## 请注意这是您的秘钥，请不要发布在公众仓库里!
        path: baidu_urls.txt ## 文本文档的地址，新链接会保存在此文本文档里
```

  ![HEXO%20SEO/Untitled%203.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf8jlu6wj30w80u0kjl.jpg)

- 自动推送的代码粘贴到相应的位置即可

``` JS
      <script>
        (function(){
            var bp = document.createElement('script');
            var curProtocol = window.location.protocol.split(':')[0];
            if (curProtocol === 'https') {
                bp.src = 'https://zz.bdstatic.com/linksubmit/push.js';        
            }
            else {
                bp.src = 'http://push.zhanzhang.baidu.com/push.js';
            }
            var s = document.getElementsByTagName("script")[0];
            s.parentNode.insertBefore(bp, s);
        })();
      </script>
```

  ![HEXO%20SEO/Untitled%204.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf8oq65mj30w90u0qv5.jpg)

- 执行hexo部署命令

``` bash
      hexo g
      hexo d
```
---

将这些配置全部走完一遍之后, 你博客的SEO优化就算完成了, 在百度上去搜可以发现可以很轻松的搜索到你的博客. 但是, 请记住SEO是一条漫长的道路, 并不是一天就能实现网站排名靠前的, 需要你不断的更新高质量多的博文, 吸引流量才可以.

### 大功告成!

![HEXO%20SEO/Untitled%205.png](https://tva1.sinaimg.cn/large/006tNbRwly1g9jf8rqebgj30y20td4iz.jpg)