---
title: 欲罢不能的主题：Tranquilpeak
date: 2016-11-19
tags: Theme
category: Other
keywords:
- hexo
- Theme
comments: true
---
遇到一个好的主题实在不易，以前使用Jekyll，倒不是平台不好，但是自己没有一点点的前端知识储备，所有遇到喜欢但是功能不全的主题，总是心有余，力不足。没有好的主题，写的东西也很少，所以一直在寻找好的主题，也在计划慢慢开始学一下前端，做自己喜欢的东西出来，不为别的，只为喜欢，看得舒心。但也没有放弃寻找，终于在Hexo中找到了欲罢不能的主题：Tranquilpeak
<!--more-->

传送门：[Tranquilpeak](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak) ，[User Documents](https://github.com/LouisBarranqueiro/hexo-theme-tranquilpeak/blob/master/docs/user.md)

<!-- toc -->

# Node.js
使用的时候，需要下载[Node.JS](http://nodejs.cn)，在Install的时候会遇到下载太慢的问题，这时候需要更换源，只需要在终端输入：
`SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install`
这时候再输入教程中的：
`npm install hexo-cli -g`
但是还是不行，因为需要修改成这样：
`sudo npm install hexo-cli -g`

# 关于更换终端后
看到很多人都是用分支来做的，做起来比较麻烦，可以将Hexo源文件重开一个仓库来管理，然后博客仓库保存站点就可以了。这样只需要在每次更新Hexo使用Git命令上传就行了，发布文章还是按照Hexo的教程来做。
## 更换终端后的操作
记住不要重新建立Hexo，只需要执行以下几个命令就好了。
- 克隆远程Hexo仓库
- `sudo npm install hexo-cli -g`
- `npm install`
- `npm install hexo-deployer-git --save`
有可能会出现样式刷新不出来的问题，这时候就要去下载最新的主题版本了，或者，在主题文件中执行Node，但是这样的方法比较慢。

# 发布操作
如果使用了Algolia搜索，那么就不需要使用生成命令了，每次只需要使用`Hexo algolia`就好了，然后发布到远程博客仓库。

# 主题中会用到的代码
## 标题配置部分

```
title: 欲罢不能的主题：Tranquilpeak
date: 2016-11-19
tags: theme
category: Other
keywords:
- hexo
- theme
```

## 搜索配置代码

```
fields:
    - title
    - date
    - permalink
    - thumbnailImageUrl
    - tags
    - categories
    - excerpt
    - excerpt:strip
```

## 缩略图部分

```
clearReading: true
thumbnailImage: image-1.png
thumbnailImagePosition: bottom
autoThumbnailImage: yes
metaAlignment: center
coverImage: image-2.png
coverCaption: "A beautiful sunrise"
coverMeta: out
coverSize: full
coverImage: image-2.png
gallery:
    - image-3.jpg "New York"
    - image-4.png "Paris"
    - http://i.imgur.com/o9r19kD.jpg "Dubai"
    - https://example.com/orignal.jpg https://example.com/thumbnail.jpg "Sidney"
comments: false
```

## 展开全文或阅读更多

```
<!-- more --> 更多
<!-- excerpt --> 全文
<!-- toc --> 目录
```

## 醒目标记

```
// class: info/success/warning/no-icon/danger
{% alert danger no-icon %}
Here is a danger alert without icon
{% endalert %}
```

## 高亮文字

```
// red/green/blue/purple/orange/yellow/cyan/primary/success/warning/danger
{% hl_text danger %}
your highlighted text
{% endhl_text %}

// RGBA
<p>Sed imperdiet urna et quam ultrices {% hl_text rgba(12, 12, 12, 0.4) %}your highlighted text{% endhl_text %} dignissim ultrices libero.</p>
```

## 内容图片

```
{% image fancybox right clear group:travel image2.png http://google.fr/images/image125.png 150px 300px "A beautiful sunrise" %}
```

## 代码块

```
{% tabbed_codeblock example http://example.com %}
      <!-- tab js -->
          var test = 'test';
      <!-- endtab -->
      <!-- tab css -->
          .btn {
              color: red;
          }
      <!-- endtab -->
  {% endtabbed_codeblock %}
```

## 大图

```
{% wide_image http://google.fr/images/image125.png "A beautiful sunrise" %}
```

