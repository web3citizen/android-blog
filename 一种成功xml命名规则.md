>原文：[A successful XML naming convention](http://jeroenmols.com/blog/2016/03/07/resourcenaming/)

>Github:

>译者注：


![xml](http://jeroenmols.com/img/blog/resourcenaming/resourcenaming.png)

你还记得最后那次你钻进去strings.xml里面查找你正在使用的字符，或手动浏览所有drawables找到你要的那个的时间吗？

当我们开始一个新项目时，我们总是花费很多时间在架构，CI(持续集成),build flavors...等等，但是对于资源命名你准备好了策略么？

你应该准备！因为缺乏xml命名空间，让管理android资源非常麻烦，特别是在大项目，容易失控。


所以下面介绍一种简单的模式来解决你的痛苦。

- 容易查找任何资源(借助ide的自动完成)
- 逻辑，可预测的名字
- 资源清晰有序 
- 强类型资源


这篇文章会解释它的机制，它的优点，局限性，最后会提供一个简单易用的小册子。

#基本原理
所有资源命名都遵循一个简单的规则

![basic](http://jeroenmols.com/img/blog/resourcenaming/whatwheredescriptionsize.jpg)


让我们先来描述每一个元素，再看完了优点之后，我们将会看到怎么把它应用到每一种资源类型。

###WHAT(是什么)
表明资源实际代表什么，通常是一个标准的android view,

(例如:MainActivity->activity)

###WHERE(在何处)
描述它在app的逻辑模块，如果在多个页面用到使用*all*,其他的就用 使用资源的页面所处的逻辑模块

(例如:MainActiviy->main，ArticleDetailFragment->articledetail)
###DESCRIPTION(是什么)
在页面中用来区分多个元素

(例如：title)
###SIZE(大小)可选

一个精确的大小或尺寸，可用于drawables和dimensions

(例如:24dp,small)

![]()

下载和打印这个[小册子](http://jeroenmols.com/img/blog/resourcenaming/resourcenaming_cheatsheet.pdf)方便参考

#优点

**1.资源根据页面排序**

where部分描述了一个资源属于哪个页面，所以对于一个特定的页面，可以获取到它的所有ids,drawables,dimensions...等等

**2. 强类型资源ids**

资源的ids,WAHT描述了

**3. 更好的资源组织**

文件浏览器或项目导航通常把文件安装字母排序，这意味的
layouts和drawables分别安装他们的what(activity,fragment)和where进行排序,现在Android studio本身和一些插件已经支持分组了

**4. 更有效的自动补全**

因为资源名称足够预测，所以用IDE的自动补全变得更加简单，通常只需要输入what或者where就可以缩小可选项

**5. 减少名字冲突**

不同页面用到的相同的资源用all，其他的用各自的where，固定的命名模式避免了所有的命名冲突

**6. 清晰的资源名称**

**7. 工具支持**

这个命名模式可以轻松被Android Studio

#layouts
Layouts相对简单，通常一个页面只有几个layouts,所以可以简化为：
![layouts](http://jeroenmols.com/img/blog/resourcenaming/layouts.png)
what是下面表格中的一种

dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz

#strings
#drawbles
#ids
#dimensions


#需要知道的限制
**1. 页面命名必须唯一**

为了避免where冲突,View类必须有一个唯一的名字，例如你不能有"MainActivity"和"MainFragment",因为这个"main"前缀就不是一个唯一的where了
>译者：个人觉得这个问题还有一个方法可以解决，
如果你的MainFragment比MainActivity的id多的话，MainActivity->maina,MainMainFragment->main,反之MainActivitymain,MainMainFragment->mainf

**2. 不支持重构**

当进行重构时改变class名不会改变相关的资源名，所以如果你把"MainActivity"重命名成"ContentActivity"的时候，layout文件"activity_main"不会重命名成"activity_content",希望Android Studio有一天会支持。

**3. 不是所有的资源类型都支持**

该方案当前还没有支持所有的资源类型。有些资源因为很少用（例如：raw和asset），还有一些资源因为他们很难组织（例如：themes/styles/colors/animations）(译者注：思考一下 一个color white你会想去组织它么)


#总结
好了！一个清晰简单和易于使用的资源命名模式，不要忘了下载小册子方便参考哦！

尽管这个模式没法覆盖所有的资源类型，但是它确实给当前的命名之痛提供了一个易于使用的解决方法。也许在以后，我会给其他资源类型提供一些建议。

上twitter联系@molsjeroen告诉我你的想法，或者在下面留言！