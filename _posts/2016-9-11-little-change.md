---
layout: post
title:  "对于模板作者HyG原模板的一些小变动"
date:   2016-09-11 10:57 +0800
categories: 记录
tags: 记录
author: Amy Zhong
---

* content
{:toc}


HyG（交大师兄）的模板挺不错的，采用他的模板就清空一些他的信息，待me以后补充。






## 更改Demo

关于Demo补充，可于目录`js`中`waterfall.js`中这个字段中填充：

```
var demoContent = [{
        demo_link: '',
        img_link: '',
        code_link: '',
        title: '',
        core_tech: '',
        description: ''
    }]
```

## 更改collections

补充collections请于`page`目录的`3collections.md`中直接以markdown语法补充

## 编辑blog

请将您博客.md文件放到`_posts`目录下，请注意blog开头固定如下：

```
---
layout: post
title:  "XXXXXXX"
date:   2016-09-9 15:57 +0800
categories: XXX
tags: XX YYYYY
author: Amy Zhong
---

* content
{:toc}  (directory structure of this blog)

XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX(abstruct)




XXXXX(the real blog which is 4 blankline from abstruct)
```
