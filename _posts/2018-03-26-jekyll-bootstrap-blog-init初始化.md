---
layout: post
title: 博客主题
date: 2018-03-36 02:41
author: admin
comments: true
categories: [Jekyll]
tags: [Jekyll,jekyll-bootstrap-blog]
---

## New Features and Enhancements 中文支持##

Todey, `jekyll-bootstrap-blog` add an enhancement,中文支持## that pages can excluded from top bar nav(See https://github.com/waylau/jekyll-bootstrap-blog/issues/3). It's very uesful to hide the page, but you can still visit it derictly by page's url.  

<!-- more -->

The `jekyll-bootstrap-blog` is a theme for Jekyll to build a personal blog. See <https://github.com/waylau/jekyll-bootstrap-blog>


### Page excluded from top bar nav

By default, created pages will show on the top bar nav. If you want page excluded from top bar nav, you just add`excludedFromNav: true` in the page as below. For example, we want hide the `labs` page: 

```
---
layout: page
title: Labs
permalink: /labs/
excludedFromNav:true
---
```

![](/images/post/20161203/20161203-excluded-from-nav-03.png)

Now, as you see ,the `labs` page was excluded from top bar nav:

![](/images/post/20161203/20161203-excluded-from-nav-01.png)

But, you can still visit it derictly by page's url:

![](/images/post/20161203/20161203-excluded-from-nav-02.png)



## Demo

You can see the theme running on [my blog](https://waylau.com/).





