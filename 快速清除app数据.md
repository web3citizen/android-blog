>原文链接：[Clear the app data quickly](https://medium.com/sebs-top-tips/clear-the-app-data-quickly-android-studio-protips-1-ebc47ea06286#.cj23k5wx8)

我们在开发app的时候，debug时经常会遇到需要清除app数据的场景，比如清除登录用户名重新进行登录测试，清除某个列表页面的数据库数据等等。

这个操作还是挺麻烦的，需要在我们手机的文件管理或者app管理找到对应的app 然后进行数据清除操作，一次还好  要是每次都要清除数据进行调试就有点麻烦了。

有一个插件可以帮助你解决这个烦扰：**[ADB IDEA](https://github.com/pbreault/adb-idea)**,as的一个adb插件

![adb idea](http://upload-images.jianshu.io/upload_images/186157-ce350416050cca39?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面的图片可以看到操作：Tools->Android->ADB idea->ADB Clear App Data And Restart
然后你的app就会清楚数据重新启动了，比手动操作可快多了。

当然还有更快的方法：

**1**：使用快捷键： Ctrl+Shift+A (Ctrl+Alt+Shift+A Linux和 Windows)呼出弹出窗口

**2**：输入**cr** (Clear Restart)

![adb dialog](http://upload-images.jianshu.io/upload_images/186157-af883f4ac68adb2c?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后点击或者使用快捷键6就可以了。

不错 还是挺实用的插件。