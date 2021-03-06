# 后端工程师学前端(四): CSS进阶(盒子模型)

盒子模型是我们常用的一种布局模式，在盒子模型之前，一般是用表格布局，但是现在已经弃用。

> 这一篇文章主要出自阅读《CSS权威指南》之后的笔记

## 基础知识

在渲染的时候，CSS会假定每个元素都生成一个或多个矩形框，我们称之为元素框。各元素框的中心是内容区域，四周有可以选择的
内边距，边框，轮廓和外边距。

控制边距的属性，`padding` 是内边距，`margin` 是外边距。它们的值有四个属性，分别是上、右、下、左，为了方便记忆，可以这样
想，时钟的起点（0点）是在上面，顺时针转一圈就是他们的参数顺序。例如 `padding: 1px 2px 3px 4px;` 就是设置内边距，上右下左
的距离分别是 1、2、3、4像素。如果使用百分比来设置值，那么便是元素相对于父元素的宽度来计算。

对于图像等，我们一般会设置宽度，而将高度设置为 `auto` 以防止图像拉伸变形。

## 盒子模型

CSS盒子模型就是我们将每一个CSS元素都看做是一个盒子，每个盒子有内边距、边框、外边距等多个属性，只不过有些属性值为0，因此
我们看不出来。

![CSS盒子模型](./img/css_box_model.png)

- 内容区域是指真正的内容，例如文字、图片等
- 边框是指盒子模型中所说的，每个元素都在一个盒子里，而盒子就有一个边框
- 内边距是指边框到内容区域的距离
- 外边距是指边框到其它元素（例如父元素、兄弟元素）的距离

> 我一般会直接使用类似于Bootstrap之类的CSS布局框架，CSS有自带一个栅格布局，但是我个人觉得不如Bootstrap好使，因此就不介绍了。

---

参考资料：

- https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model
- https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout
