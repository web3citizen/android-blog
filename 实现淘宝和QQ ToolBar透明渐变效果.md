>[自定义CoordinatorLayout的Behavior实现知乎和简书快速返回效果
](http://www.jianshu.com/p/d372d37e8640)

每天用淘宝和QQ 会发现淘宝的商品详情页和qq的好友动态页都不约而同的用了工具栏透明渐变效果，淘宝是为了不挡住商品图片，qq设置为了不挡住header image背景，效果感觉还挺好看，老有人问怎么做的。

那我们来分析一下他们的效果

**淘宝效果:**图片向上滑动的时候，toolbar慢慢的由透明变成完全不透明，向下滑动则相反

![淘宝效果](http://upload-images.jianshu.io/upload_images/186157-3ebabd7564a4d264?imageMogr2/auto-orient/strip)

**QQ效果:**(请自行查看手机，模拟器打死都安装不了，录不了gif) qq的效果类似不过有点不同  下面会分析

![QQ效果](http://upload-images.jianshu.io/upload_images/186157-59b644d4978dadec?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看完他们的效果我们可以发现：

- 页面进来 toolbar开始都是透明的
- 向上滑动 toolbar的透明度由透明变成透明，再向下滑动时，又由不透明变成透明

但是仔细体验会发现它们还是有一点点不同，qq的好友动态是要滑到快要出现文字时才会瞬间变成不透明，看下面的图大概能理解了(数字代表alpha 0为完全透明 0-255表示0-100的透明度  255表示完全不透明)

![效果区分](http://upload-images.jianshu.io/upload_images/186157-7912905e04746727?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到QQ的渐变区间小的多 也就是一瞬间就变了透明度

好了 简单看了别人家的效果  下面我们要自己来简单实现一下了  

###1.布局

淘宝和QQ的布局总体看都是一样的  toolbar +可滑动内容区(header+列表)

    <?xml version="1.0" encoding="utf-8"?>
    <android.support.design.widget.CoordinatorLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
    
        <android.support.v4.widget.NestedScrollView
            android:layout_width="match_parent"
            android:layout_height="match_parent">
    
            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical">
    
                <ImageView
                    android:id="@+id/header"
                    android:layout_width="match_parent"
                    android:layout_height="@dimen/header_height" //图片高度为250dp
                    android:scaleType="centerCrop"
                    android:src="@mipmap/header"/>
    
                <TextView
                    style="@style/TextAppearance.AppCompat.Display2"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="你的状态\n朋友1的状态\n朋友2的状态\n朋友3的状态\n朋友4的状态\n朋友5的状态\n朋友6的状态\n朋友7的状态\n朋友8的状态\n朋友9的状态\n朋友10的状态\n朋友11的状态\n朋友12的状态\n朋友13的状态\n朋友14的状态\n朋友15的状态\n朋友16的状态"/>
            </LinearLayout>
        </android.support.v4.widget.NestedScrollView>
    
    
        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar_behavior"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:layout_behavior="liuwp.com.attendance.activity.ToolbarAlphaBehavior"
            />
    
    
    </android.support.design.widget.CoordinatorLayout>

上面可滑动部分为了简单起见用了NestedScrollView,当然你可以可以换成RecyleView,里面包含了一个固定高度的图片和文字列表  很简单的布局。

###2.实现自定义的ToolbarAlphaBehavior

原理就是根据滑动的偏移值来设置对应的toolbar透明度（看上面的渐变区间分析图一目了然）  实现也就几行代码（就喜欢几行代码的感觉）

    public class ToolbarAlphaBehavior extends CoordinatorLayout.Behavior<Toolbar> {
        private static final String TAG = "ToolbarAlphaBehavior";
        private int offset = 0;
        private int startOffset = 0;
        private int endOffset = 0;
        private Context context;
    
        public ToolbarAlphaBehavior(Context context, AttributeSet attrs) {
            super(context, attrs);
            this.context = context;
        }
    
        @Override
        public boolean onStartNestedScroll(CoordinatorLayout coordinatorLayout, Toolbar child, View directTargetChild, View target, int nestedScrollAxes) {
            return true;
        }
    

        @Override
        public void onNestedScroll(CoordinatorLayout coordinatorLayout, Toolbar toolbar, View target, int dxConsumed, int dyConsumed, int dxUnconsumed, int dyUnconsumed) {
            startOffset = 0;
            endOffset = context.getResources().getDimensionPixelOffset(R.dimen.header_height) - toolbar.getHeight();
            offset += dyConsumed;
            if (offset <= startOffset) {  //alpha为0
                toolbar.getBackground().setAlpha(0);
            } else if (offset > startOffset && offset < endOffset) { //alpha为0到255
                float precent = (float) (offset - startOffset) / endOffset;
                int alpha = Math.round(precent * 255);
                toolbar.getBackground().setAlpha(alpha);
            } else if (offset >= endOffset) {  //alpha为255
                toolbar.getBackground().setAlpha(255);
            }
        }
    
    }

没错就这么几行代码，主要代码都在onNestedScroll方法里面  

###3.配置Behavior

给Toolbar配置Behavior

     <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar_behavior"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:layout_behavior="liuwp.com.attendance.activity.ToolbarAlphaBehavior" //添加配置
            />

因为一开始页面进来toolbar就是透明的 别忘了初始化透明度

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_behavior);
            Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar_behavior);
            toolbar.getBackground().setAlpha(0);//toolbar透明度初始化为0
        }

然后就ok了 

###4.结束

最后看下我们的效果

![toolbar渐变效果](http://upload-images.jianshu.io/upload_images/186157-f45022413ab28ba8?imageMogr2/auto-orient/strip)

恩 还不错 是不是觉得分分钟就搞定了 当然这里只是简易实现 给大家一个思路，用在实际产品开发中 亲 可能要考虑更多东西哦。