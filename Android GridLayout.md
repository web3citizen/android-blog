>原文：[Android Grid Layout](https://medium.com/google-developer-experts/android-grid-layout-1faf0df8d6f2#.ldtru2yec)

###*android开发者每天都在问自己一个问题-我到底应该用哪个layout*

GridLayout发布已经有一段时间了-新的布局组件：**Space**和**GridLayout**

然而GridLayout在当前开发中的情况如下：

- 大多数开发者并不知道这个布局
- 一些开发者知道GridLayout但是基于某些原因不去使用
- 只有少部分开发者花时间了解和积极使用

这是我为什么要写这篇文章的原因，因为我认为这个布局不应该被不公平遗忘

#为什么我们需要Grid Layout
GridLayout可以让你用一个简单的根view创建一个表格系统的布局
>我可以用LinearLayout嵌套来实现

是可以做到，但是你会有性能问题当布局嵌套太深

>我可以用RelativeLayout来创建

也行，但是RelativeLayout有一些限制，例如：
- 没法基于轴线对齐
- 组件会超出屏幕当他们需要的空间因为你不能使用weight等等

换一句话说就是RelativeLayout不够灵活和响应性不足。

#例子

让我们实现一个简单的布局包含一个大图片，2个小图标和跟在图标的文本

![Preview]()

###*RelativeLayout*

用RelatieveLayout实现起来非常简单，通过关键属性*layout_below*,*layout_toRightOf*和*layout_alignTop*

![Code]()

一眼看上去好像很完美，直到你用不同字体size进行布局测试

*Issue 1*

单行文本应该相对于图标垂直居中，不幸的是RelativeLayout没有提供这个可能性

![Preview]()

*Issue 2* 组件重叠

多行文本会引起重叠，因为text用了layout_alignTop对图标进行对齐

![Preview]()

###*GridLayout*

如你看到的下面图片一样，GridLayout提供更好的表现结果：

- 文本垂直居中于图标
- 多行文本不会移动组件

![Preview]()

那么怎么实现这个效果呢？首先定义GridLayout为根布局。然后计算你要多少列并通过android:columnCount属性定义，在我们的例子中我们有2列。

当你定义GridLayout里面的views时因为是一个接一个被放置，所以没必要明确定义view root和column

如果你想撑开view占用2行或2列，你可以用*layout——columnSpan*/*layout_rowSpan*属性

还有一定要记住-如果你想你的view使用所有可用的空间，不要设置设置width为match_parent，设置成0dp同时设置属性layout_gravity="fill"

![Code]()


#总结

GridLayout一方面是一个非常强大的工具，它提供了很大的灵活性和性能，另外一方面它需要一些时间来学习了解它如何工作，通常你需要花更多的时间来开发和维护这样的布局。















