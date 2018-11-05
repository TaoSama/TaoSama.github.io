---
title: Hello World —— 博客搭建历程
date: 2016-03-09 00:27:42
categories: 
- Doing
- Blog
- 
tags: 博客
toc: true
---

~~Updating~~

## 搭建博客
使用`hexo`生成静态博客并架设在免费的`github pages`平台
详情看这篇博客，[零基础免费搭建个人博客-hexo+github](http://hifor.net/2015/07/01/%E9%9B%B6%E5%9F%BA%E7%A1%80%E5%85%8D%E8%B4%B9%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2-hexo-github/)

<!--more-->

## 主题选择
搞了半天，研究了`jacman，next，...`，最后发现还是自带的`landscape`挺好的，然后就用了`landscape-plus`
准备在此基础上改改，借鉴了[这个人](http://shijiajie.com/2015/08/29/hexo-theme-landscape-plus-optimize/)的修改姿势，并自己添加了一些东西
* 修改归档挂件的时间格式：（左边为原图，右边为效果图）
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/80562236.jpg)

   * 路径: `\themes\landscape-plus\layout\_widget\archive.ejs` 第`5`行
   ```
      <%- list_archives({format: 'YYYY年 MM月'}) %>

   ```
---
* 修改归档页面的时间格式：（左边为原图，右边为效果图）
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/97283281.jpg)

   * 路径：`\themes\landscape-plus\layout\_partial\archive-post.ejs` 第`4`行
   ```
         <%- partial('post/date', {class_name: 'archive-article-date', date_format: 'MM-DD'}) %>
   ```
---
* 添加新挂件，推荐文章：
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/51498733.jpg)

  * 路径：`\themes\landscape-plus\layout\_widget\archive\recent_comments.ejs` 新文件
  ```
<% if (site.posts.length){ %>
  <div class="widget-wrap">
    <h3 class="widget-title"><%= __('recent_comments') %></h3>
    <div class="widget">
      <!-- 多说最新评论 start -->
      <div class="ds-recent-comments" data-num-items="5" data-show-avatars="0" data-show-time="1" data-show-title="1" data-show-admin="1" data-excerpt-length="70"></div>
      <!-- 多说最新评论 end -->
      <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
      <script type="text/javascript">
        var duoshuoQuery = {short_name:"Taos"};
        (function() {
          var ds = document.createElement('script');
          ds.type = 'text/javascript';ds.async = true;
          ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
          ds.charset = 'UTF-8';
          (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
        })();
      </script>
      <!-- 多说公共JS代码 end -->
    </div>
  </div>
<% } %>
```
---
* 添加新挂件，最近评论：
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/28683113.jpg)

  * 路径：`\themes\landscape-plus\layout\_widget\archive\recent_comments.ejs` 新文件
  ```
<% if (site.posts.length){ %>
  <div class="widget-wrap">
    <h3 class="widget-title"><%= __('recent_comments') %></h3>
    <div class="widget">
      <!-- 多说最新评论 start -->
      <div class="ds-recent-comments" data-num-items="5" data-show-avatars="0" data-show-time="1" data-show-title="1" data-show-admin="1" data-excerpt-length="70"></div>
      <!-- 多说最新评论 end -->
      <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
      <script type="text/javascript">
        var duoshuoQuery = {short_name:"Taos"};
        (function() {
          var ds = document.createElement('script');
          ds.type = 'text/javascript';ds.async = true;
          ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
          ds.charset = 'UTF-8';
          (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
        })();
      </script>
      <!-- 多说公共JS代码 end -->
    </div>
  </div>
<% } %>
```

 * <a>添加最近评论挂件样式表</a>
 路径：`\themes\landscape-plus\source\css\_partial\sidebar-aside.styl` 末尾添加
```
.ds-recent-comments
  margin: -16px 0 0 0
  a
    color: color-link
```
---
* 文章结尾增加声明文字：
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/82348825.jpg)

 * 路径：`\themes\landscape-plus\layout\_partial\post\statement.ejs` 新文件
 ```
 <% if (!index && page.source != 'about/index.md'){ %>
   <div class="article-statement">
     <hr> 
	 <!-- 内容 -->
   </div>
 <% } %>
 ```

 * <a>添加声明文字样式表</a>
 路径：`\themes\landscape-plus\source\css\_partial\sidebar.styl` 末尾添加
    ```
.article-statement
  font-size: 0.85em;
  line-height: 1.6em;
  margin: 0.5em 0;
  text-shadow: 0 1px #fff;
  color: #999;
  a
    &:hover
      text-decoration: none;
```
---
* 页脚中添加访问统计、为超链接添加target="_blank"属性：
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/55063221.jpg)

   * 路径：`\themes\landscape-plus-bak\layout\_partial\footer.ejs` 修改为
```
<footer id="footer">
  <script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"> </script>
  <% if (theme.sidebar === 'bottom'){ %>
    <%- partial('_partial/sidebar') %>
  <% } %>
  <div class="outer">
    <div id="footer-info" class="inner" align = "center">
      Copyright &copy; <%= date(new Date(), 'YYYY') %> 
	  <a href="/" target="_blank"> <%= config.author || config.title %> </a> <br>
      Powered by <a href="https://hexo.io/zh-cn/docs/index.html" target="_blank">Hexo</a>
	  &nbsp;|&nbsp;
      Theme by <a href="https://github.com/xiangming/landscape-plus" target="_blank">landscape-plus</a>
	  &nbsp;|&nbsp;
	  <span id="busuanzi_container_site_pv">
        总访问量 <a href="http://service.ibruce.info/" target="_blank"><span id="busuanzi_value_site_pv"></span></a> 次
      </span>
      <span id="busuanzi_container_site_uv">
        <a href="http://service.ibruce.info/" target="_blank"><span id="busuanzi_value_site_uv"></span></a> 人
      </span>  
    </div>
  </div>
    <!-- 为超链接加上target='_blank'属性 -->
	<script type="text/javascript">
		$(document).ready(function() {
			$('a[href^="http"]').each(function() {
			$(this).attr('target', '_blank');
		});
	});
	</script>
</footer>
```
---
* 文章添加生成目录开关
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/29113644.jpg)

   * 路径：`\themes\landscape-plus\layout\_partial\article.ejs` 搜索`<%- post.content %>`前面添加
```
<!-- Table of Contents -->
<% if (!index && post.toc){ %>
  <div id="toc" class="toc-article">
    <strong class="toc-title">Contents</strong>
    <%- toc(post.content) %>
  </div>
<% } %>
```

   * <a>添加目录样式表</a>
    路径：`\themes\landscape-plus\source\css\_partial\article.styl` 末尾添加
    ```
.toc-article
  background #eee
  border 1px solid #bbb
  border-radius 10px
  margin 1.5em 0 0.3em 1.5em
  padding 1.2em 1em 0 1em
  max-width 28%
.toc-title
  font-size 120%
#toc
  line-height 1em
  font-size 0.9em
  float right
  .toc
    padding 0
    margin 1em
    line-height 1.8em
    li
      list-style-type none
  .toc-child 
    margin-left 1em
```

   * <a>启用目录</a>
   在需要添加文章目录的的博文`md`文件中添加开关`toc: true`
   如果不喜欢链接的颜色，可以编辑`\themes\landscape-plus\source\css\_variables.styl` 第`6`行
   ```
   color-link = #E32D40
   ```
* 文章标题添加一条灰色分割线
![](http://7xru22.com1.z0.glb.clouddn.com/16-3-14/4653222.jpg)

 * 路径：`\themes\landscape-plus\source\css\_partial\article.styl` 第`26`行
 ```
 .article-header
  padding: article-padding
  border-bottom: 2px solid color-border
 ```

## 问题处理
* Marked渲染markdown与Mathjax渲染latex冲突
路径：`\node_modules\marked\lib\marked.js`
 * 第`451`行
```
escape: /^\\([\\`*{}\[\]()# +\-.!_>])/,
修改为：
escape: /^\\([`*\[\]()# +\-.!_>])/,
```
 * 第`854`行
```
return '<em>' + text + '</em>';
修改为：
return '_' + text + '_';
```
* 图床和云空间选择
[极简图床](http://yotuku.cn/#!/)配合[七牛云](http://www.qiniu.com),感觉挺棒的
