---
layout: post
title: "给GitHub Pages上的Jekyll站点添加标签支持"
tags: Jekyll GitHubPages
date: 2017-08-28 21:45:00 +0800
---
## 起因
[minima](https://github.com/jekyll/minima)主题虽说很简洁，但是功能也太少了，连最基本的归档和标签都没有支持。所以我想给我的博客加上这些功能，并且把它们放在一个侧边栏里。

添加一个右侧侧边栏，并且使它能够自动适配PC/移动端的排版，并不是一件难事，真正的问题，是如何给现有的主题添加标签功能。
## 现有的Jekyll插件
Jekyll的一大特点就是插件多，但是，GitHub Pages出于安全考虑，除了少数几个插件以外，不允许使用自定义插件，于是这条路就走不通了。
## 自己来实现
### 生成标签列表
Jekyll使用Liquid模板来生成静态页面，并且已经提供了`site.tags`变量用于访问标签以及标签对应的文章列表，所以显示出一个[标签-文章数]列表，可以很简单地完成（这个例子里面没有加入超链接）：

    <ul>
    { % for tag in site.tags % }
    { { tag[0] | escape } } ( { { tag[1].size } } )
    { % endfor % }
    </ul>

剩下的问题就是，如何为每个标签生成一个页面，列出这个标签下的所有文章？

Google一番之后，我了解到不少人都是让所有标签显示在一个页面上，并且列出所有标签下的所有文章。但是我并不喜欢这种做法，这样不仅会把页面拉的超长，还会让同一篇文章多次出现（如果它有多个标签的话），实在不是一个很好的体验。

所以，我选择给每个标签都生成一个页面。
### 生成标签页面
首先要为标签页面写一个layout，排版很简单，根据指定的标签名称，列出文章列表就好。我用的layout在[这里](https://github.com/zerozwt/zerozwt.github.io/blob/master/_layouts/tag_page.html)。

搞定了layout之后，真正麻烦的事情在于为每个标签生成一个使用指定layout的页面，Google到的文章说的都是要自己手工完成，但是……老子是程序员啊！这种明显应该机器做的事情让老子手工做？！Are you kidding me？

所以我就写了一个[python脚本](https://github.com/zerozwt/zerozwt.github.io/blob/master/gen_pages.py)，执行的时候会扫描`_posts`目录下的所有文件，提取YAML头信息里的tags字段，收集标签，并且在`tags`目录下为每个标签生成一个html文件，这样Jekyll在生成站点的时候就可以为我的每个标签生成单独的页面了。
## 所谓的最后一公里
事情做到这份上，每次写文章以后的操作就只是两个命令：

1. python gen_pages.py
2. bundle exec jekyll serve

但是还是不够啊……为啥要输入两个命令啊……于是，往~/.bashrc里面加入一行：

    alias buildjekyll='python gen_pages.py && bundle exec jekyll serve'

然后每次`buildjekyll`就完事了，真正解决了“最后一公里”的问题。
## 接下去想做的事
其实这种用脚本扫描`_posts`下的文件的做法不仅可以用来搞定标签功能，归档也可以用类似的思路搞。所以下一个目标就是实现按年的归档功能。

更新：不到一个小时，按年归档功能就完成了。