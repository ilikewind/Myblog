---
layout: post
title: "HTML+CSS书写规范、顺序和命名规则"
subtitle: ""
catalog: ture
author: "转载"
header-style: text
tags: 
    - Web
---

## CSS书写顺序

1. 位置属性(position, top, right, z-index, display, float等)
2. 大小(width, height, padding, margin)
3. 文字系列(font, line-height, letter-spacing, color- text-align等)
4. 背景(background, border等)
5. 其他(animation, transition等)

<!--break-->

![](/img/in-post/2019/html_01.png)

## CSS书写规范 

1. 使用CSS缩写属性

   CSS有些属性是可以缩写的，比如padding,margin,font等等，这样精简代码同时又能提高用户的阅读体验。

![](/img/in-post/2019/html_02.png)

2. 去掉小数点前的“0”

![](/img/in-post/2019/html_03.png)

3. 简写命名

   很多用户都喜欢简写类名，但前提是要让人看懂你的命名才能简写哦！

4. 16进制颜色代码缩写

   有些颜色代码是可以缩写的，我们就尽量缩写吧，提高用户体验为主。

5. 连字符CSS选择器命名规范

   1. 长名称或词组可以使用中横线来为选择器命名。

   2. 不建议使用“_”下划线来命名CSS选择器,why?

      输入的时候少按一个shift键； 浏览器兼容问题 （比如使用\_tips的选择器命名，在IE6是无效的） 能良好区分JavaScript变量命名（JS变量命名是用“_”）

6. 不要随意使用id

   id在JS是唯一的，不能多次使用，而使用class类选择器却可以重复使用，另外id的优先级优先与class，所以id应该按需使用，而不能滥用。

7. 为选择器添加状态前缀

   有时候可以给选择器添加一个表示状态的前缀，让语义更明了，比如添加了“.is-”前缀。

## CSS命名规则

### 常用的CSS命名规则

头：header 

内容：content/container

 尾：footer 

导航：nav 

侧栏：sidebar 

栏目：column 

页面外围控制整体布局宽度：wrapper 

左右中：left right center 

登录条：loginbar 

标志：logo 

广告：banner 

页面主体：main 

热点：hot 

新闻：news 

下载：download 

子导航：subnav 

菜单：menu 

子菜单：submenu 

搜索：search 

友情链接：friendlink 

页脚：footer 

版权：copyright 

滚动：scroll 

内容：content 

标签：tags 

文章列表：list 

提示信息：msg 

小技巧：tips 

栏目标题：title 

加入：joinus 

指南：guide 

服务：service 

注册：regsiter 

状态：status 

投票：vote 

合作伙伴：partner

注释的写法: /\* Header \*/ 
==内容区== 
/\* End Header \*/

### 页面结构

容器: container 

页头：header 

内容：content/container 

页面主体：main 

页尾：footer 

导航：nav 

侧栏：sidebar 

栏目：column 

页面外围控制整体布局宽度：wrapper 

左右中：left right center

### 导航

导航：nav 

主导航：mainnav 

子导航：subnav 

顶导航：topnav 

边导航：sidebar 

左导航：leftsidebar 

右导航：rightsidebar 

菜单：menu 

子菜单：submenu 

标题: title 

摘要: summary

### 功能

标志：logo 

广告：banner 

登陆：login 

登录条：loginbar 

注册：register 

搜索：search 

功能区：shop 

标题：title 

加入：joinus 

状态：status 

按钮：btn 

滚动：scroll 

标籤页：tab 

文章列表：list 

提示信息：msg 

当前的: current 

小技巧：tips 

图标: icon 

注释：note 

指南：guild 

服务：service 

热点：hot 

新闻：news 

下载：download 

投票：vote 

合作伙伴：partner 

友情链接：link 

版权：copyright

## CSS样式表文件命名

主要的 master.css 

模块 module.css 

基本共用 base.css 

布局、版面 layout.css 

主题 themes.css 

专栏 columns.css 

文字 font.css 

表单 forms.css 

补丁 mend.css 

打印 print.css

## 文件命名

  head.asp 头文件

foot.asp 底文件

index.asp 首页文件

sort.html 分类嵌套文件

article_channel.asp 文章_频道页

article_list.asp 文章_列表页

article_detail.asp 文章_显示页

注明：如果有多个文章频道，则用article01， article02，article03等等

exhibit_channel.asp 展会信息_频道页

exhibit_list.asp 展会信息_列表页

exhibit_detail.asp 展会信息_显示页

product_channel.asp 产品中心_频道页

product_list.asp 产品中心_列表页

prodect_detail.asp 产品中心_显示页

corporation_channel.asp 会员_频道页

corporation_list.asp 会员_列表页

corporation_detail.asp 会员_显示页

information_channel.asp 商机信息_频道页

information_list.asp 商机信息_列表页

information_detail.asp 商机信息_显示页

job_channel.asp 招聘_频道页

job_list.asp 招聘_列表页

job_detail.asp 招聘_显示页

hr_channel.asp 求职_频道页

hr_list.asp 求职_列表页

hr_detail.asp 求职_显示页

job_hr_channel.asp 人才中心_频道页

job_hr_lisr.asp 人才中心_列表页

job_hr_detail.asp 人才中心显示页

copyright.asp 版权页  

## 图片命名

 导航命名：menu.gif 如：menubg .gif（导航的背景图）

会员登录：login.gif 如：loginbg.gif （会员登陆的背景图）

搜索命名：search.gif 如：search_bg.gif （搜索的背景图）

小 图 标：ico_数字.gif 如：ico_001.gif

线的命名：line_X_颜色.gif 如：line_X_red.gif（红色横向虚线）说明：line只命名虚线，line_Y_red.gif（红色纵向虚线）

广告命名：ad_数字.gif 如：ad001.gif

其它栏目的图片：以栏目名的第一个字母.gif 如：xwzx_bg.gif （新闻中心背景），cpzx_l.gif （产品中心的左边图）

产品与栏目热点图片： pic_数字.gif 如：pic_001.gif 说明：上、下、左、右 可以缩写为T、B、L、R  