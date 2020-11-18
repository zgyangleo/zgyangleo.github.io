---
layout: post
title:  "Ubuntu环境下jekyll预览效果"
date:   2019-06-30 10:54:01 +0800
categories: jekyll
tag: jekyll
---

* content
{:toc}


[转](#)
[Ubuntu环境下jekyll预览效果](https://fleschier.github.io/2018/07/08/Jekyll-usage/)

[环境安装](#)

确保下面环境的齐全：

        brew install ruby

        gem install jekyll

        gem install bundler

        gem install jekyll-paginate

        gem install jekyll-gist

如果执行命令的过程中有提示什么未安装， 按照提示的命令来安装即可。

然后将终端切换到你博客所在的根目录，执行： 
        jekyll serve --watch

终端中会告诉你博客使用了本地4000端口， 在网页中输入： https://127.0.0.1:4000/ 即可预览博客的效果

更新本地内容保存后更新网页即可看到效果