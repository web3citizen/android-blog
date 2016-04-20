>原文：[A successful XML naming convention](http://jeroenmols.com/blog/2016/03/07/resourcenaming/)

>Github:

>译者注：命名是一个项目的小细节，也是一个项目的基石。虽然我们懂得了各种命名规范，但是我必须承认最后我每次都失败了，实践到最后就会发现做好真的很难，除了疯涨的资源文件，还涉及到不同的团队成员，就算同一个控件，有可能每个人都能给上不同的名字。这篇文章给了一个不错的模式，可以结合自己的情况进行改造，结合之前那篇项目结构的文章会有不错的效果。命名规范重要，但是每个人做到遵循更为重要。


![xml](http://jeroenmols.com/img/blog/resourcenaming/resourcenaming.png)

你还记得最后一次你钻进去strings.xml里面查找你正在使用的字符，或手动浏览所有drawables找到你要的那个是什么时候吗？

当我们开始一个新项目时，我们总是花费很多时间在架构，CI(持续集成),build flavors...上面，但是对于资源命名你准备好了策略么？

你应该准备！因为缺乏xml命名空间，让管理android资源非常麻烦，特别是在大项目，容易失控。


所以让我们引入一个简单的模式来解决你的痛苦。

- 容易查找任何资源(借助ide的自动完成)
- 逻辑，可预测的名字
- 资源清晰有序 
- 强类型资源


这篇文章会解释它的机制，它的优点，局限性，最后会提供一个简单易用的速查表。

#基本原理
所有资源命名都遵循一个简单的规则

![basic](http://jeroenmols.com/img/blog/resourcenaming/whatwheredescriptionsize.jpg)


让我们先来描述每一个元素，再看完了优点之后，我们将会看到怎么把它应用到每一种资源类型。

###WHAT(是什么)
表明资源实际代表什么，通常是一个标准的android view,资源类型选项有限。

(例如:MainActivity->activity)

###WHERE(在何处)
描述它在app的逻辑模块，如果在多个页面用到使用*all*,其他的就用 使用该资源的页面所处的逻辑模块

(例如:MainActiviy->main，ArticleDetailFragment->articledetail)
###DESCRIPTION(描述)
用来区分一个页面中多个相同元素

(例如：title)
###SIZE(大小)可选

一个精确的大小或尺寸，可用于drawables和dimensions

(例如:24dp,small)

![]()

下载和打印这个[速查表](http://jeroenmols.com/img/blog/resourcenaming/resourcenaming_cheatsheet.pdf)方便参考

#优点

**1.资源根据页面排序**

where部分描述了一个资源属于哪个页面，所以对于一个特定的页面，可以获取到它的所有ids,drawables,dimensions...等等
>译者注：例如main页面，你只要输入main就可以找到所有的资源了。

**2. 强类型资源ids**

对于资源的ids,what描述了xml元素所属的类名。这样你调用findViewById()的时候知道转换成什么。
>译者注：textview_main这个id你马上就知道它的控件class是TextView了

**3. 更好的资源组织**

文件浏览器或项目导航通常把文件安装字母排序，这意味的
layouts和drawables分别按照他们的what(activity,fragment)和where进行分组,现在Android studio本身和一些插件已经支持了。

**4. 更有效的自动补全**

因为资源名称足够预测，所以用IDE的自动补全变得更加简单，通常只需要输入what或者where就可以缩小可选项。

**5. 减少名字冲突**

不同页面用到的相同的资源用all，其他的用各自的where，固定的命名模式避免了所有的命名冲突

**6. 清晰的资源名称**

整体上把所有的资源命名更加逻辑，让整个项目非常清晰。

**7. 工具支持**

这个命名模式可以轻松被Android Studio提供的一些功能支持，例如：强制执行lint规则，支持当你改变what或where进行重构时，在project view更好的资源可视化。。。


#layouts
Layouts相对简单，通常一个页面只有几个layouts,所以规则可以简化为：
![layouts](http://jeroenmols.com/img/blog/resourcenaming/layouts.png)
what是下面表格中的一种

 前缀	 | 用法
---------|------
activity | activity的内容视图  
fragment | fragment的view
view 	 | 用于inflate的自定义View
item	 | 用于list/recycle/gridview的item布局
layout	 | 用于include标签使用的布局

例子：

- activiy_main:MainActivity的content view
- fragment_articledetail:ArticleDetailFragment的view
- view_menu:在自定义MenuView类中被inflated的布局
- layout_actionbar_backbutton:一个包含返回按钮的actionbar工具栏布局(过于简单没有必要封装成一个自定义view,直接include)

#strings
Strings的what部分不重要，没有什么用处的（译者注：因为string.xml已经固定了它的what，drawables是一样的道理）,所以我们要么用**where**来指明这个字符在只在某个页面被用到：
![strings_where](http://jeroenmols.com/img/blog/resourcenaming/strings.png)
要么用**all**表示在app中多个地方会重用：
![strings_all](http://jeroenmols.com/img/blog/resourcenaming/strings2.png)
例子：

- articledetail_title:ArticleDetailFragment页面的标题
- feedback_explanation:FeedbackFragment反馈页面的说明
- feedback_namehint:FeedbackFragment页面的name输入框的提示语
- all_done:通用的“done”字符

很明显在同一个view里面所有资源的where是一样的。

#drawables

drawables的what部分也是没用的，所以我们也是要么用where指明在哪个页面用到：
![drawables_where](http://jeroenmols.com/img/blog/resourcenaming/drawables.png)
要么用all表示在app中重用的：
![drawables_all](http://jeroenmols.com/img/blog/resourcenaming/drawables2.png)
你也可以添加可选的size部分，可以用来表示实际大小24dp或尺寸small。

例子：

- articledetail_placeholder:ArticleDetailFragment的占位图
- all_infoicon:通用info图标
- all_infoicon_large:通用info图标的大图
- all_infoicon_24dp:24dp大小的通用info图标


#ids
对于ids,what是xml元素所属类名。第二个部分是id所在的页面，紧接后面的是一个可选的描述用来区分一个页面的多个相同元素。
![ids](http://jeroenmols.com/img/blog/resourcenaming/ids.png)
例子：

- tablayout_main：MainActivity里面的TabLayout控件
- imageview_menu_profile:自定义MenuView中的用户ImageView
- textview_articledetail_title:ArticleDetailFragment中的标题TextView

>译者注：这里的what可以自己换一下，改成控件类名的首字母 例如：textview->tv会简洁一点，不然有时候会真的很长。

#dimensions
app应该只定义一组有限的diemens,它们必须要被不断重复使用。这样大多数deimensions默认就是all

所以你应该多用：
![dimens_all](http://jeroenmols.com/img/blog/resourcenaming/dimensions2.png)
当然特定页面也可以使用：
![demens_spec](http://jeroenmols.com/img/blog/resourcenaming/dimensions.png)

这里的what是下面表格中的一种：

 前缀	 | 用法
---------|------
width 	 | 宽度  
height   | 高度
size 	 | 宽度=高度
margin	 | margin外边距
padding	 | padding内边距
elevation| 角度
keyline	 | 边缘对齐位置
textsize | 文本大小的sp

注意上面的列表包含了大部分会用到的what,还有一些dimension限定符类似：rotation,scale...通常只是在drawables被用到而且不能重用。

例子：
>译者注：all不用声明，所以默认是all

- height_toolbar:所有toolbar的高度
- keyline_listtext:listitem的文本边缘对齐
- textsize_medium:所有文本的中等大小
- size_menu_icon:menu里面图标size
- height_menu_profileimage:menu的头像图片高度






#需要知道的限制
**1. 页面命名必须唯一**

为了避免where冲突,View类必须有一个唯一的名字，例如你不能有"MainActivity"和"MainFragment",因为这个"main"前缀就不是一个唯一的where了
>译者：个人觉得这个问题有一个小方法可以解决，
如果你的MainFragment比MainActivity的id多的话，MainActivity->maina,MainMainFragment->main,反之MainActivity->main,MainMainFragment->mainf

**2. 不支持重构**

当进行重构时改变class名不会改变相关的资源名，所以如果你把"MainActivity"重命名成"ContentActivity"的时候，layout文件"activity_main"不会重命名成"activity_content",希望Android Studio有一天会支持。

**3. 不是所有的资源类型都支持**

该方案当前还没有支持所有的资源类型。有些资源因为很少用（例如：raw和asset），还有一些资源因为他们很难组织（例如：themes/styles/colors/animations）(译者注：color和animation都是偏向使用术语来进行命名，而theme和style已经有了命名规则，并且允许你隐性继承属性)




#总结
好了！一个清晰简单和易于使用的资源命名模式，不要忘了下载[速查表](http://jeroenmols.com/img/blog/resourcenaming/resourcenaming_cheatsheet.pdf)方便参考哦！

尽管这个模式没法覆盖所有的资源类型，但是它确实给当前的命名之痛提供了一个易于使用的解决方法。也许在以后，我会给其他资源类型提供一些建议。

上twitter联系@molsjeroen告诉我你的想法，或者在下面留言！
