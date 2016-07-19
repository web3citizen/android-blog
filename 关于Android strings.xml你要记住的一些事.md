>原文：[Android strings.xml — things to remember](https://medium.com/@dmytrodanylyk/android-strings-xml-things-to-remember-c155025bb8bb#.jjmb7gqpq)

>译文的GitHub地址：[关于Android strings.xml你要记住的一些事](https://github.com/thinkSky1206/android-blog/blob/master/%E5%85%B3%E4%BA%8EAndroid%20strings.xml%E4%BD%A0%E8%A6%81%E8%AE%B0%E4%BD%8F%E7%9A%84%E4%B8%80%E4%BA%9B%E4%BA%8B.md)

>译者注：都是一些很实用的技巧 ，尤其是对于多语言国际化开发，感同身受 。

这篇文章是关于android开发再平常不过的东西--strings.xml

#不要复用

>不要在多个页面复用字符串

**1.**假设你在登录和注册页面都有一个loading加载框，你打算让2个加载框使用同一个字符-R.string.loading.

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/reuse01.png)

不久后你决定让其中一个加载框换个字符，然后你不得不创建2个新的字符串并修改你的java代码，如果你一开始就用了2个string,你就只需要修改一个strings.xml文件就可以了
![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/reuse02.png)

**2.**你永远不知道你的app将来会支持哪种语言，在某个语言-你可能可以用同一个单词表达不同的内容(译者：done可以表达中文的确定，下一步等等)，但是在另外一个语言-你不得不用多个单词表达不同的内容。

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/reuse03.png)

![res/values-UA/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/reuse04.png)

可以看到英语版本的strings.xml用了同样的单词 “YES”给R.string.download_file_yes 和 R.string.terms_of_use_yes

但是乌克兰版本的strings.xml使用了2个不同单词 “Гаразд”给R.string.download_file_yes，“Так”给R.string.terms_of_use_yes。


#分开
>用前缀和注释让同一个页面的字符分开

![res/values-UA/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/sep01.png)

**1.**给每个字符添加页面前缀用于帮助识别当前字符属于哪个界面

**2.**清晰的strings.xml可以让维护变的简单，同时可以一个页面接一个页面翻译成其他语言

#Format格式化
>使用Resources#getString(int id, Object… formatArgs)格式化字符串

千万别用“+”这种操作符，因为在不同语言单词排列顺序可能是不一样的

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/format01.png)


![java code](https://github.com/thinkSky1206/android-blog/blob/master/images/format02.png)


正确的方式是使用 Resources#getString(int id, Object… formatArgs)

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/format03.png)


![res/values-UA/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/format04.png)


![java code](https://github.com/thinkSky1206/android-blog/blob/master/images/format05.png)


#Plurals复数
>使用Resources#getQuantityString (int id, int quantity)获取数量字符串

不要在你的java代码里面手动解析复数，因为不同语言有不同的规则和数量语法协议。

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/plu01.png)


![java code](https://github.com/thinkSky1206/android-blog/blob/master/images/plu02.png)

正确的方式是使用Resources#getQuantityString (int id, int quantity)


![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/plu03.png)


![java code](https://github.com/thinkSky1206/android-blog/blob/master/images/plu04.png)


#字符高亮
>使用html文本高亮静态字符

如果你想改变TextView字符串的颜色-*ForegroundColorSpan*并不是一个任何时候都好用的一个选择，因为高亮是通过下标index来实现的，在多语言app里面并不安全（译者：你要手动计算不同语言同一个单词的index,容易数组下标越界）。更好的做法是使用strings.xml里面的html font颜色标签。


假设你有一个文本“Discover and play games.” 然后你想高亮“Discover”单词和“play”单词成蓝色。

![res/values/strings.xml](https://github.com/thinkSky1206/android-blog/blob/master/images/wh01.png)

![java code](https://github.com/thinkSky1206/android-blog/blob/master/images/wh02.png)




