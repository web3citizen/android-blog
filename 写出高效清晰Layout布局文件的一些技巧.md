>原文：[Android – How to write Batman like xml layout](http://ivankocijan.xyz/android-batman_like_layout/)

当人们谈论Android性能的时候总是习惯讨论怎么写出清晰高效的Java代码,却忽略了layout布局文件。layout布局缓慢的渲染速度对app性能也有的很大的影响。充满不必要的views和可读性差的layout文件会让你的app运行缓慢。在本文中我会分享5个技巧来帮你写出高效清晰的layout布局文件。(ps:下面的技巧都非常实用，开发过程中很常见，感动哭！)

###1. Use compound drawable on a TextView
>用TextView本身的属性同时显示图片和文字

(ps:难以理解现在还有很多人不懂得用这个，实实在在的减少很多view啊，哎！)

通常你需要在文本旁边添加一张图片，假设你需要添加图片在文字的左边，像下面这样：

![TextView](http://upload-images.jianshu.io/upload_images/186157-7d1534318daf150e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不少人首先想到的就是用一个LinearLayout或RelativeLayou来包含一个TextView和ImageView,最后你用了三个UI元素和一大坨代码。用TextView的compound drawable是一个更好更清晰的解决方案。你只需要一个属性就可以搞定。

    <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/batman"
            android:drawableLeft="@drawable/batman"
            android:drawableStart="@drawable/batman"
            android:drawablePadding="5dp">
    </TextView>

用到的主要属性：

**drawableLeft**-  指定drawable放在文本的左边

**drawableStart**-  作用和drawableLeft相同但是它基于新的API(android 4.2)支持[RTL](http://android-developers.blogspot.hr/2013/03/native-rtl-support-in-android-42.html)
 
**drawablePadding**- 指定文本和drawable之间padding

###2. ImageView has src and background attribute
>同时使用ImageView的src和background属性实现点击效果

你应该同时使用它们，在很多情况下你会想要给ImageView添加点击效果，然后我看到很多人用LinearLayout来包裹一个ImageView来实现。添加另一个view是没必要的。下面的代码可以让你做的更好：

    <ImageView
            android:id="@+id/image"
            android:layout_width="@dimen/batman_logo_width"
            android:layout_height="@dimen/batman_logo_height"
            android:background="?attr/selectableItemBackground"//点击效果
            android:src="@drawable/batman_logo_transparent"//图片
            style="@style/logo_image_style"/>

显示图片用"src"属性，drawable selector 图片点击效果用"background"属性实现，上面用的是android默认提供的selector,当然你也可以换成你自己实现的。下面是最后的效果：(ps:哈哈，效果自己copy上面几行代码就可以看到了，实在需要看请翻墙查看原文)。

###3. Use LinearLayout divider
>用LinearLayout自带的分割线

分割线在app经常会用到的，使用频率高到让你惊讶。但是LinearLayout有一个属性可以帮你添加分割线。下面的例子中，LinearLayout包含2个TextView和基于他们中间的分割线。

![divider](http://upload-images.jianshu.io/upload_images/186157-c908d64cefdc08c3?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######1.Create divider shape(创建shape)
下面是一个简单的shape divider_horizontal.xml用来当做分割线。

    <?xml version="1.0" encoding="utf-8"?>
    <shape xmlns:android="http://schemas.android.com/apk/res/android">
     
        <size android:width="@dimen/divider_width"/>
        <solid android:color="@color/colorPrimaryDark"/>
     
    </shape>

######2.Add shape to LinearLayout
    <LinearLayout android:layout_width="match_parent"
                  android:layout_height="wrap_content"
                  android:orientation="horizontal"
                  android:background="@android:color/white"
                  android:divider="@drawable/divider_horizontal"  //添加分割线
                  android:dividerPadding="5dp" //设置padding
                  android:showDividers="middle">//居中显示
     
     
        <TextView android:layout_width="0dp"
                  android:layout_weight="0.5"
                  android:layout_height="wrap_content"
                  android:gravity="center"
                  style="@style/Text.Title"
                  android:text="@string/batman_name"/>
     
        <TextView android:layout_width="0dp"
                  android:layout_height="wrap_content"
                  android:layout_weight="0.5"
                  android:gravity="center"
                  style="@style/Text.Title"
                  android:text="@string/superman_name"/>
     
    </LinearLayout>

上面用到了三个xml属性：

**divider** -用来定义一个drawable或者color作为分割线

**showDividers** -指定分割线在哪里显示，它们可以显示在开始位置，中间，末尾或者选择不显示

**dividerPadding** -给divider添加padding

###4.Use the Space view
>使用Space控件

当你需要在2个UI控件添加间距的时候,你可能会添加padding或margin。有时最终的layout文件是非常混乱，可读性非常差。当你需要解决问题时，你突然意识到这里有一个5dp的paddingTop,那里有一个2dp的marginBottom,还有一个4dp的paddingBottom在第三个控件上然后你很难弄明白到底是哪个控件导致的问题。还有我发现有些人在2个控件之间添加LinearLayout或View来解决这个问题，看起来是一个很简单解决方案但是对App的性能有很大的影响。

这里有一个更简单更容易的方法那就是Space,看下官方的文档：*“Space is a lightweight View subclass that may be used to create gaps between components in general purpose layouts.”* 他们没有说谎，确实很轻量。如果你看过Space的实现会发现Space继承View但是没有绘制任何东西在canvas。

    /**
     * Draw nothing.
     *
     * @param canvas an unused parameter.
     */
    @Override
    public void draw(Canvas canvas) {
    }

使用方法很简单，看下面的图片，我们想要在在标题和描述之间添加间距。

![space](http://upload-images.jianshu.io/upload_images/186157-a4b03fba72448034?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你只需要简单的在2个TextView之间添加一个Space就可以了

    <TextView android:layout_width="match_parent"
              android:layout_height="wrap_content"
              style="@style/Text.Title"
     
    <Space android:layout_width="match_parent"
           android:layout_height="10dp"/>//添加间距
     
    <TextView android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:text="@string/gotham_city_description"
              style="@style/Text.Details"/>

###5.Use <include/> and <merge/>
>使用<include/>和<merge/>标签

重用布局是一个保持app一致的好方法，这样以后有改变的话只要改一个文件就可以了，Android提供了<include/>标签帮你重用布局。

例如你现在决定创建有一个logo图片居中的酷炫Toolbar工具栏，然后你想要添加到每个页面中，下面是Toolbar效果:

![toolbar](http://upload-images.jianshu.io/upload_images/186157-741b82214cf2be0e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
下面是batman_toolbar.xml代码

    <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/AppTheme.AppBarOverlay"
            app:popupTheme="@style/AppTheme.PopupOverlay">
     
     
        <ImageView android:layout_width="wrap_content"
                   android:layout_height="@dimen/batman_logo_height"
                   android:layout_gravity="center"
                   android:src="@drawable/batman_logo_transparent"/>
     
    </android.support.v7.widget.Toolbar>

你可以复制粘贴这些代码到每个Activity,但是不要这么做，在编程中有一个重要的规则：当你复制粘贴，那你就是在做错误的事。在这种情况下你可以用<include/>标签在多个界面重用这个布局。

    <?xml version="1.0" encoding="utf-8"?>
    <android.support.design.widget.CoordinatorLayout
            xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:fitsSystemWindows="true"
            android:background="@android:color/white"
            tools:context=".MainActivity">
     
        <include layout="@layout/batman_toolbar"/>
     
    </android.support.design.widget.CoordinatorLayout>

用<include/>标签你可以只用一行代码在app的每个页面添加你的toolbar,任何改变都会自动更新到所有页面。

除了<include/>,<merge/>也常用来从你的view层次结构中减不必要的view，它会移除没必要的嵌套的layouts，例如，如果被包含布局文件的根是一个LinearLayout,然后你把它include包含在另外一个LinearLayout里面，2个嵌套的LinearLayouts是没有必要的，这个嵌套的layout没有任何作用除了影响UI性能。在这种情况下可以用<merage/\>来替换被包含布局的根LinarLayout 移除不必要的view.

关于<include/>和<merge/>的更多信息你可以查看[官方文档](http://developer.android.com/training/improving-layouts/reusing-layouts.html)

###Don’t always play by the rules
>上面的技巧不用当做规则

我希望这5个技巧可以帮你写出更好更简单的布局layout,但是不要把这些技巧当是规则，它们更像是指南。总有一些情况下你没法使用这些技巧，只能通过增加布局的复杂性来解决。在这种情况下，在添加更多view之前，你可以考虑自定义View试的找到更简单的解决方法。只要记住一件事，在你的视图层次添加一个view代价是不可小嘘的，它会影响你的app加载速度。

谢谢Ana和oFca对文章进行校对。