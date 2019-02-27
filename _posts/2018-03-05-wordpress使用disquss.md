---
layout: post
title: wordpress使用disquss
categories: [技术] 
description: wordpress使用disquss
keywords: h5
---

...安装好disqus插件，发现disqus竟然被墙... 然后找到一个利用 PHP cURL 转发 Disqus API 请求 用于墙内环境访问 Disqus的项目.

插件自动安装
======

*   墙内可忽略此方法...装好了也无法加载.
*   在https://disqus.com/ 注册账号，根据提示安装.

错误
--

*   按照步骤，装插件启用后打开报： "The plugin was unable to generate a secret key for your site. You will have to create one below in order for installation and syncing to work." 然后手动生成key ，点击保存没有反应？？？
    
*   打开控制台查看 请求url：`https://www.ggbond.cc/index.php/wp-json/disqus/v1/settings` 返回404错误，搜索得知wp-json 是wordpress的rest api. wordpress4.7+已经自动集成了，百度全是如何禁用...可是我没有禁用过.
    
*   请求其它rest api都没有反应，问题定位到rest api 被禁用. 由于我用的caddy做服务器,谷歌得到[这个](https://caddy.community/t/caddy-and-wordpress-rest-api/2479) 可能是url重写的问题。 检查配置文件没有毛病...
    
*   很好。原来是我装的WPJAM插件...有个性能优化.‘关掉wordpress中一些不常用的功能’...我打上了勾. 果然是它禁用了rest api.........................................
    

使用 Disqus PHP API
=================

*   感觉disqus还可以啊，不能就这么算了. 找到这个项目: [](https://github.com/fooleap/disqus-php-api)就用它吧... 实现原理可参考:[科学使用disqus](http://blog.fooleap.org/use-disqus-correctly.html#id-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-disqus-api)

简单说是通过国外服务器代理请求disqus api 获取数据. 首先关闭disqus插件

后端
--

1.  在国外服务器部署 `disqus-php-api/api/` 项目( 如果同我一样wordpress也部署到国外服务器.可直接利用wordpress部署, clone整个项目到博客主题目录...)
2.  补充config.php相关配置

前端
--

1.  前台用到 `dist/iDisqus.min.css` 和 `dist/iDisqus.min.js`两个文件 下载到wordpres所在服务器(上面已clone可跳过)
    
2.  进入到当前使用主题目录`wp-content/themes/***` 先备份`comments.php` `header.php`
    
3.  修改`header.php` 引入js和css文件
    
```html
    <link rel="stylesheet" href="path to your /iDisqus.min.css" />
    <script src="path to your  /iDisqus.min.js"></script>
    
```

4.  修改comments.php如下:

```html
      <div id="comment"></div>
      <script>
                var disq = new iDisqus('comment', {
                    forum: '',  // disqus短名
                    api: '', //后端地址 (如果使用wordpress部署为:http://www.example.com/wp-content/themes/*****)
                    site: '', //网站url
                    mode: 2, //参考项目readme
                    timeout: 3000,
                    init: true
                });
        </script>
    
```

bug
---

*   装好后竟然无法显示之前导入的评论... 查看后台 https://****.disqus.com/admin/discussions/ 发现原来disqus是以url定位文章拉取评论的...我地址为www.ggbond.cc，填成了ggbond.cc

完成
==

安装成功后，可以参考 https://import.disqus.com/ 导入之前的评论.
