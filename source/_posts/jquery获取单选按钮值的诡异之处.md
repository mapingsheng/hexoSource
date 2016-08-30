---
title: jquery获取单选按钮值的诡异之处
date: 2016-08-21 12:17:10
categories: javascript
tags: jquery
---
最近在使用jquery获取表单中选中的单选按钮的值时，发现了一个诡异的问题，那就是当你使用[checked]这个方式获取选中的单选按钮值时，能不能获取选中的值跟你前面用的选择器方式有关系，当你使用:radio[name='types']这种方式获取选中的值时是没有问题的，但是当你使用[type='radio'][name='language']这种方式获取选中的值时是有问题的（如果单选按钮组中有默认选中的按钮，那么这种方式一直获取的都是默认选中的按钮；如果单选按钮组中没有默认选中的按钮，那么这种方式获取不到选中的单选按钮的值）。举例如下：

表单元素如下：
```java
	<input type="radio" name="language"  value="php"/>
	<input type="radio" name="language"  value="mysql"/>
	<input type="radio" name="language"  value="java"/>
```

**一、方式一 [checked] (正确)**

通过使用`[checked]`获取选中的单选按钮的值，类型选择器使用的`:radio`

    $('input:radio[name="language"][checked]').val()；

![Alt text](http://soujava.com/images/jqueryRadio2.png "可以获取单选值")

*注：这种方式`可以`获取选中的单选按钮的值，如果没有选择任何单选按钮，则获取的值为undefined。*


**二、方式二 [checked] (错误)**

通过使用`[checked]`获取选中的单选按钮的值，，类型选择器使用的`[type="radio"]`

    $('input[type="radio"][name="language"][checked]').val()；

![Alt text](http://soujava.com/images/jqueryRadio1.png "不可以获取单选值")

*注：这种方式`不可以`获取选中的单选按钮的值，获取的值始终为undefined。*


**三、小结 **

上面两种获取单选按钮选择中的方式中唯一差别的地方就是类型选择器的方式一种是标准的类型选择器(type="radio")、一种是伪类选择器(:radio);然后到jquery官方网站查看官网文档可以看到：

![Alt text](http://soujava.com/images/jqueryRadio3.png "官方文档")

官网文档中关于选择器的描述大致这样说：$(":radio")和$("[type=radio]")是等价的,在效率方面还更倾向使用标准的类型选择器


**四、方式三 :checked (没有任何问题)**

使用`[type="radio"]`类型选择器：

    $('input[type="radio"][name="language"]:checked').val()；

使用`:radio`伪类选择器：

    $('input[type="radio"][name="language"]:checked').val()；

![Alt text](http://soujava.com/images/jqueryRadio4.png "获取值")

*注：方式三种中在使用:checked方式的前提下，不管使用类型选择器还是使用伪类选择器都是可以获取选中的单选按钮的值的！*

**五、总结**

获取单选按钮元素选中的值时，统一采用`:checked`这种方式获取，不要再使用`[checked]`或者`[checked="checked"]`这种方式。