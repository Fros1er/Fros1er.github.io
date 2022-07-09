---
title: hexo-deploy
date: 2022-07-09 22:24:02
tags:
---

参考：
https://hexo.io/docs/github-pages
https://github.com/YunYouJun/hexo-theme-yun/discussions/103
https://blog.csdn.net/f554236884/article/details/105870070

我是真没想到配个hexo的CI配了两个小时。。在这记一下

## Github Actions
结论：`actions/checkout@v2`（包括v1和v3）的submodule不好用（也许是我哪里配错了，但最后也不成功）。  
来来回回照着各种地方的配置文件配了一遍，都报`WARN no layout`。  
报错原因倒是很好找，在CI里`ls themes`是空的。  
最后是用的[这里](https://github.com/YunYouJun/hexo-theme-yun/discussions/103)的配置，然后手动clone了一下next主题。非常的不优雅，但是没别的办法了。。

## 域名
这个没啥好说的，cloudflare配一下CNAME，github settings里改一下，source目录里面写个CNAME完事。
