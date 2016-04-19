>原文地址：[Top 5 Android libraries every Android developer should know about - v. 2015](https://infinum.co/the-capsized-eight/articles/top-five-android-libraries-every-android-developer-should-know-about-v2015)



  在2014年的六月我们发布过一篇文章关于我们正在使用并且觉得每个android开发都应该了解知道的[ top 5 Android libraries](https://infinum.co/the-capsized-eight/articles/top-5-android-libraries-every-android-developer-should-know-about)，到现在，Android开发场景又有了一些变化，所以我们重新更新了我们喜欢的三方库列表。
 ps:下面我只是列出来2个年份的表单,原文里面的的内容基本就不翻译了没太大意义，会加上我个人的一些理解。

##1.  Retrofit
retrofit已经更新到2.0版本了，架构进行了重大改变，里面的各个组件模块全部进行了解耦独立出来，估计是因为功能越来越多，为了后期的维护和开发，从一入门android一直在用。今年我开发的项目都给升级到了2.0
##2.  DBFlow
sqlite的orm,数据存储这块android目前应该是2大阵营，非sqlite的Realm和sqlite的众多orm,Realm同时支持ios和android2大平台，速度看起来好像不错，但是目前版本还没有到1.0加上pojo有限制就没有应用到项目中，目前我用的是ActiveAndroid，之前一直用不到数据库这块 ，所以选择orm的时候没有考虑太多，ActiveAndroid用起来很简单，基本满足需求，但是看了其他ORM贴出的性能比拼，数据量大时估计会考虑换下，看了一下DBFlow，下次可以考虑换上
##3.  Glide
这一年图片加载库真是热闹 ，Glide,Picasso,Fresco 让人不好选择，所幸的是开发项目没有图片需求，所以没有机会去真正使用了。
##4.  Butterknife
一直在用，很方便
##5.  Dagger 2
研究和学习了一段时间，简单写过demo,但是没有应用到项目中，新手上手难度比较大，因为我接触过Spring，所以对于理解依赖注入有帮助，测试和mvp的不二选择，如果你的项目足够大和复杂，又上了mvp,那dagger2估计是少不了。


[2014版](https://infinum.co/the-capsized-eight/articles/top-5-android-libraries-every-android-developer-should-know-about)
###1.  Gson
###2.  Retrofit
###3.  Eventbus
###4.  ActiveAndroid
###5.  Universal Image Loader



####总结
 2份列表中的大部分库我都已经应用到项目中了，节省了不少开发时间。so 快去更新你的三方库吧。