>原文：[Android strings.xml — things to remember](https://medium.com/@dmytrodanylyk/android-strings-xml-things-to-remember-c155025bb8bb#.jjmb7gqpq)

>译文的GitHub地址：[关于Android strings.xml你要记住的一些事](https://github.com/thinkSky1206/android-blog/blob/master/%E5%85%B3%E4%BA%8EAndroid%20strings.xml%E4%BD%A0%E8%A6%81%E8%AE%B0%E4%BD%8F%E7%9A%84%E4%B8%80%E4%BA%9B%E4%BA%8B.md)

>译者注：都是一些很实用的技巧 ，尤其是对于多语言国际化开发，感同身受 。

[Android应用启动优化:一种DelayLoad的实现和原理(上篇)](http://androidperformance.com/2015/11/18/Android-app-lunch-optimize-delay-load.html)


[Android端应用秒开优化体验](http://zhengxiaoyong.me/2016/07/18/Android%E7%AB%AF%E5%BA%94%E7%94%A8%E7%A7%92%E5%BC%80%E4%BC%98%E5%8C%96%E4%BD%93%E9%AA%8C/)


[Android性能优化之Splash页应该这样设计](http://zhengxiaoyong.me/2016/07/18/Android%E7%AB%AF%E5%BA%94%E7%94%A8%E7%A7%92%E5%BC%80%E4%BC%98%E5%8C%96%E4%BD%93%E9%AA%8C/)


main()->Application:attachBaseContext()->onCreate()->Activity:onCreate()->onStart()->onResume()->performTraversals(layout mesure)->performTraversals(draw绘制)（显示第一帧）


1.点击图标——application.onCreate

2.oncreate-activity第一帧

