---
layout: post
title: "Octopress写博客"
date: 2014-03-22 16:18:36 +0800
comments: true
categories: Octopress
#下面为新增内容 
description: "Octopress github 搭建博客" 
keywords: Octopress github
---
 <h4> 1.安装ruby</h4>
 <h4> 2.安装Octopress</h4>
 上面2步网上很多资料<a href="http://blog.segmentfault.com/yaashion_xiang/1190000000364677">传送门</a>
 <h4> 3.部署到github</h4>
 
 
      rake setup_github_pages
  
   按照提示输入github用户名密码，输入以下命令部署博客,push到github的master分支
    
  
      rake generate
      rake deploy
<!--more-->  
   将source单独push到github的source分支下
     
      git add .
      git commit -m 'Initial source commit'
      git push origin source
   
   部署前可以在本地预览，输入rake preview之后在浏览器输入http://localhost:4000/ 
  <h4> 4.写博客</h4>
  
      rake new_post["myTitle"]
      
   文章生成在目录下的source/_posts目录下。文章是markdown格式的。
   然后使用支持markdown语法的编辑器就可以形如流水的写博客。
   此博客是在Ubuntu ReText写的。