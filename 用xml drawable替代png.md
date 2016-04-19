>原文：[Android dev tip: Replace PNG assets with XML drawables](http://jebware.com/blog/?p=304?utm_source=androiddevdigest)
>
>译者注：我个人在开发中还是比较喜欢用xml drawable的方式来替代一些可以被替代的图片 ，看的n多图片就闹心。

Android开发中解决OutOfMemoryException问题最好方法是永远不要产生它们，所以限制app使用内存是非常重要的。有时候这意味要涉及到设计师。让我用一个案例来解释为什么呢。。。

![button](http://upload-images.jianshu.io/upload_images/186157-ac6071c9b5fce02c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你的设计师给了你一个png像上面的设计

小事一件，就是300*100dp的图片。然后放进界面中，运行app，一切都正常运行，直到你查看Android Studio的内存监视器Memory Monitor然后注意到你的内存飙升了大概1MB.

![mem jump](http://upload-images.jianshu.io/upload_images/186157-ba7d6aca6e63c955.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###发生了什么？

![finder small](http://upload-images.jianshu.io/upload_images/186157-13c45023f03aac34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你看一下你刚才添加的png,只有10kb,心想：应该不是它引起的吧？不，就是它。你在文件夹里面看到是压缩的图片文件，但是Android加载它的时候已经不是压缩文件了。它会渲染你的png成Bitmap,它以非压缩的方式存储图片。就是每个像素颜色的一个大矩阵。默认，Android用ARGB_8888格式就是每个像素4个字节来处理。是的，现在看看XXHDPI文件夹，我们的300dp事实上是900像素，这意味的我们的“小图片” 900*300转换成Bitmap时用了1080000字节(1.03MB)

![mem byte](http://upload-images.jianshu.io/upload_images/186157-56f6228bc6b25459.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好吧，非常糟糕

那么现在摆在眼前的是：1MB快要让你的app内存溢出了，然后你要告诉你的设计师说你们这群渣渣，快要弄死我的APP了？（不要这样做,容易干架）

有一个更好的方式实现这个按钮界面，那就是用XML Drawable和一个Button

    <?xml version="1.0" encoding="utf-8"?>
    <shape xmlns:android="http://schemas.android.com/apk/res/android"
        android:shape="rectangle"
        >
        <corners android:radius="10dp" />
        <solid android:color="#FFFFFF" />
        <stroke
            android:color="#000000"
            android:width="5dp"
            />
    </shape>

    <Button
            android:layout_width="300dp"
            android:layout_height="100dp"
            android:layout_gravity="center_horizontal"
            android:background="@drawable/button_xml"
            android:text="I'm A Button!"
            android:textSize="24sp"
            android:textAllCaps="false"
            />

让我们再来看一下占用内存

![xml jump](http://jebware.com/blog/wp-content/uploads/2016/03/xml-tiny-jump.png)

XML drawable只用了504字节内存。太好了

###最后总结

显然，并不是每个文件都可以用这种方式替代的，一旦遇到一些基本的形状最好用xml替代它。这样当你面对不可避免的会OutOfMemoryException，你起码延迟了出现的时间。

![not today](http://jebware.com/blog/wp-content/uploads/2016/03/4865622-1.jpg)
