---
title: Jquery中prop和attr使用时的问题
date: 2017-02-08 13:25:25
categories: javascript
tags: jquery
---
在使用jquery的attr属性处理单选按钮选中时，出现偶尔不能选中的问题，后来才知道jquery官方推荐使用prop，而再推荐使用attr。

<!--more-->

```java
 $("input[type='radio'][name='workerType'][value='IT']").attr('checked',true);
```
使用attr方法操作单选按钮为选中状态时，会出现不能选中的情况（偶现）


```java
 $("input[type='radio'][name='workerType'][value='IT']").prop('checked',true);
```
使用prop方法操作单选按钮为选中状态时，可以正常选中


当在页面中用jquery来控制DOM元素中的属性时要用 .prop()；不要再用 .attr()，不然会出问题，官方文档也推荐这样