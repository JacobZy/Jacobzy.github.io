---
layout:     post
title:      "使用jekyll和github pages构建blog"
subtitle:   "Use jekyll and github pages to make a static blog"
date:       2017-09-21 12:00:00
author:     "Jacob"
header-img: "img/postbg/post-bg-rwd.jpg"
header-mask: 0.3
catalog:    true
tags:
    - blog
    - web
---


> 使用jekyll和github pages构建blog


github提供支持构建博客的github pages，可以使用jekyll生成。

## 过程简介

1. 以邮箱申请[Github](https://pages.github.com/)账户，两种类型可选择：
- User or organization site
- Project site

2. 从现有适用博客GIT库直接fork到自己的分支，建议参考库如下：
- Github pages推荐的jekyll博客[Github jekyll sites list][2]
- [黄玄的博客][1]· [博客主页·huxpro.github.io](https://huxpro.github.io)
- [我的博客库][3]

3. 修改库名为自己的GIT库名，并修改configure文件等，可参考：
- [利用 GitHub Pages 快速搭建个人博客][4]

## 注意问题

1. 关于使用Disqus，首先在Disqus上注册账户或者用其它平台账户登入。并在[账户管理页面](https://disqus.com/admin/)填写之前建立的pages站点URL和web短名称。在_config.yml文件中修改如下部分，将disqus_username: 填写为Disqus中的web短名称。注意，不要填写成用户名称。
> #Disqus settings  
> disqus_username: webshotname

2. 为管理方便，建议使用git工具将远程pages库在本地clone，并可安装jekyll进行即时预览和修改。
- Github pages关于本地管理方法的[介绍][5]  
- jekyll本地预览命令：
> jekyll s


[1]: https://github.com/Huxpro/huxpro.github.io
[2]: https://github.com/jekyll/jekyll/wiki/sites
[3]: https://github.com/jacobzy/jacobzy.github.io
[4]: http://www.jianshu.com/p/e68fba58f75c
[5]: https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/

