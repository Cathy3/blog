---
title: Hexo常用命令
date: 2018-06-21 
categories: 
		- 计算机
tags:  
		- 命令		
---
<div align=life> 
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=350 height=100 src="//music.163.com/outchain/player?type=2&id=36492392&auto=1&height=66"></iframe>
</div>

`hexo init <folder>` 创建并初始化一个站点文件夹
`hexo new 'article title'`  新增一篇文章

`hexo clean`  用于主题切换等涉及到站点整体布局效果改变时清除hexo原有缓存

# 文章编辑完后
- 开启本地服务器，测试静态页面的效果： `hexo server`  或 `hexo s` 
- 为文章自动生成静态页面的文件：`hexo generate` 或 `hexo g` 
- 将本地页面部署到GitHub Pages上：`hexo deploy`  或 `hexo d` 
- (合并替换前两项)生成静态页面的文件并直接部署：`hexo generate -d` 或`hexo deploy -g` 