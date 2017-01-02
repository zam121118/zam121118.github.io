---
layout: post
title: 搭建github博客
date: 2016-9-11 15:35:27
categories: Git
tags: Git Markdown
author: Amy Zhong
---

* content
{:toc}


记录搭建github博客及使用模板时遇到的问题。






## 搭建过程可参考

[利用github pages建博客](http://cyzus.github.io/2015/06/21/github-build-blog/)
[Jekyll Themes模板](http://jekyllthemes.org/)


### 换上主题模板

从github上将模板克隆下来后，将仓库文件夹下的除了.git文件外全部删除，放入Jekyll的模板。
目录结构如下：

```

- index.html：这是你博客的主页面，里面的内容就是你的主页了

- _config.yml：这是你博客的基本配置文件，里面有你博客的名字，以及存放博主的一些基本信息

- _layouts：这文件夹里面存放你每个页面的设计，一般有default.html（默认页面）和posts.html（博文页面）

- _includes：这个文件夹里的的内容将会通用到你博客每个页面，起到一种便利的作用

- _posts：这里面装的就是你的博文啦，记住，要用markdown语法写，要不上传会失败的。

```

### 写博文

在_posts中使用`markdown` 语法写博文，本地使用 `jekyll` 渲染后可按预期显示后，使用 `git push origin` 提交变动。

### 查看blog

浏览器输入`https://zam121118.github.io` （换成你的github账号）查看新提交的博文。

## 使用HyG的模板

[具体修改可参看HyG项目readme](https://github.com/Gaohaoyang/gaohaoyang.github.io/blob/master/README-zh-cn.md)

### 本地渲染

若要在本地渲染博文，请预装 `ruby` 、`jekyll` , windows用户可参考[此文](http://www.open-open.com/lib/view/open1456893770562.html) 。

### 添加评论系统

可以使用[多说评论系统](http://duoshuo.com/) ，注册后，返回的代码记住自己的 `short_name` 。

### 添加百度统计

[注册百度统计](http://tongji.baidu.com/web/register)

生成如下的个人标示：
`hm.src = "//hm.baidu.com/hm.js?xxxxxxxxxxxx"` 即 `xxxxx字符串`

```
<script>
var _hmt = _hmt || [];
(function() {
  var hm = document.createElement("script");
  hm.src = "//hm.baidu.com/hm.js?5eee93cbacd99ad9c8333398cdabeb53";
  var s = document.getElementsByTagName("script")[0];
  s.parentNode.insertBefore(hm, s);
})();
</script>
```

### 在_ports中写博文

关于本地渲染过程可以参考：[How jekyll works!!!](http://jekyllbootstrap.com/lessons/jekyll-introduction.html)
注意： [post文章头格式参考： YAML Front Matter](http://jekyllrb.com/docs/frontmatter/)

* 博文编码格式

![a.png-33.9kB](http://static.zybuluo.com/maorongrong/fk14vm1og6s67su1817n8h6z/a.png)

NOTE： 我之前blog写在作业部落中，一键导出功能导出的.md文件是utf-8 BOM编码格式。所以渲染会出问题，文件编码需转为 `utf-8 without BOM `; 不想找麻烦请将blog文件名全英。

* 博文文件名

> _posts This folder contains your dynamic content/posts. the naming format is required to be ` @YEAR-MONTH-DATE-title.MARKUP@`. If the data format is invalid Jekyll will not recognize the file as a post.

> **The date and title are automatically parsed from the filename of the post file.** Additionally, each file must have `YAML Front-Matter` prepended to its content.

> `YAML Front-Matter` is a valid YAML syntax specifying meta-data for the given file.

`.md` 文件开头格式如下：

```
---
layout: post
title:  XXXXXXXXXXXXXX
date:   2016-03-12 11:40:18
categories: AAA
tags: AAA BBB  CCCC
author: XXX XX
---
                  #here has 1 blank lines
* content
{:toc}            #this is used to generate directory




abstruct........  # abstruct is 4 lines away from directory


                  #here has 2 blank lines
```

### 更改Jekyll服务端口

本机若安装福晰阅读器，会与 `jekyll` 默认服务端口冲突，更改服务端口为1234

```
# port
port: 1234
```

### 本地执行

进入本地github博客仓库,在该目录下执行 `jekyll serve` 启动 `jekyll `， 浏览器键入 `http://127.0.0.1:1234/` 查看本地渲染效果。

## 自定义主题信息

[参见github--原模板小改动](https://zam121118.github.io/)

### 修改网站ico

在 [bitbug](http://www.bitbug.net/) 在线生成你的网站小ico（16X16 is okay）。

fork[HyG博客模板](https://github.com/Gaohaoyang),在`_includes`目录下的 `head.html` 文件中更改你自己的ico名称

```
<link rel="shortcut icon" href="/myfavico.ico?" type="image/x-icon">
<link rel="icon" href="/myfavico.ico?" type="image/x-icon">
<link rel="stylesheet" href="{{ "/css/fontawesome/css/font-awesome.min.css " | prepend: site.baseurl }}>"
```
将`myfavico.ico`换成你的名字。


  [1]: http://static.zybuluo.com/maorongrong/fk14vm1og6s67su1817n8h6z/a.png
  [2]: http://static.zybuluo.com/maorongrong/63178t35zr5olxiy8hfhtver/aaaa.png
