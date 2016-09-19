

>译文的GitHub地址：[RecyclerView之ItemDecoration由浅入深](https://github.com/thinkSky1206/android-blog/blob/master/RecyclerView%E4%B9%8BItemDecoration%E7%94%B1%E6%B5%85%E5%85%A5%E6%B7%B1.md)

>译者注：RecyclerView第一篇，希望后面坚持下来 

RecyclerView没有像之前ListView提供divider属性，而是提供了方法

	recyclerView.addItemDecoration()
其中ItemDecoration需要我们自己去定制重写，一开始可能有人会觉得麻烦不好用，最后你会发现这种可插拔设计不仅好用，而且功能强大。


ItemDecoration类主要是三个方法：

	public void onDraw(Canvas c, RecyclerView parent, State state)
	public void onDrawOver(Canvas c, RecyclerView parent, State state)
	public void getItemOffsets(Rect outRect, View view, RecyclerView parent, State state)


官方源码虽然都写的很清楚，但还不少小伙伴不知道怎么理解，怎么用或用哪个方法，下面我画个简单的图来帮你们理解一下。

![ItemDecoration](http://upload-images.jianshu.io/upload_images/186157-1ab2f9304503506c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图画的丑请见谅，首先我们假设绿色区域代表的是我们的内容，红色区域代表我们自己绘制的装饰，可以看到：

图1：代表了getItemOffsets(),可以实现类似padding的效果

图2：代表了onDraw(),可以实现类似绘制背景的效果，内容在上面

图3：代表了onDrawOver()，可以绘制在内容的上面，覆盖内容

注意上面是我个人从应用角度的看法，事实上实现上面的效果可能三个方法每个方法都可以实现。只不过这种方法更好理解。

下面是我们没有添加任何ItemDecoration的界面

![activity](http://upload-images.jianshu.io/upload_images/186157-e8eafc2e1dd17110.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

主页布局界面很简单，背景设成灰色

	<?xml version="1.0" encoding="utf-8"?>
	<android.support.design.widget.CoordinatorLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    android:background="@color/gray">//灰色背景
	
	
	    <android.support.design.widget.AppBarLayout
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content">
	
	        <android.support.v7.widget.Toolbar
	            android:id="@+id/toolbar"
	            android:layout_width="match_parent"
	            android:layout_height="?attr/actionBarSize"
	            android:background="?attr/colorPrimary"
	            app:layout_scrollFlags="scroll|enterAlways"/>
	
	    </android.support.design.widget.AppBarLayout>
	
	    <android.support.v7.widget.RecyclerView
	        android:id="@+id/recycler_view"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        app:layout_behavior="@string/appbar_scrolling_view_behavior"
	        />
	
	</android.support.design.widget.CoordinatorLayout>

ok 接下来，让我们来实现实际开发中常遇到的场景。


#padding
从前面的图可以看到实现这个效果，需要重写getItemOffsets方法。

	public class SimplePaddingDecoration extends RecyclerView.ItemDecoration {
	
	    private int dividerHeight;
	
	
	    public SimplePaddingDecoration(Context context) {
	        dividerHeight = context.getResources().getDimensionPixelSize(R.dimen.divider_height);
	    }
	
	    @Override
	    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
	        super.getItemOffsets(outRect, view, parent, state);
	        outRect.bottom = dividerHeight;//类似加了一个bottom padding
	    }
	}

没错，就这么2行代码，然后添加到RecyclerView

	recyclerView.addItemDecoration(new SimplePaddingDecoration(this));

实现效果：

![Padding ItemDecoration.png](http://upload-images.jianshu.io/upload_images/186157-5404e1be7bf508c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#分割线
分割线在app中是经常用到的，用ItemDecoration怎么实现呢，其实上面padding改成1dp就实现了分割线的效果，但是分割线的颜色只能是背景灰色，所以不能用这种方法。

要实现分割线效果需要 getItemOffsets()和 onDraw()2个方法，首先用 getItemOffsets给item下方空出一定高度的空间（例子中是1dp），然后用onDraw绘制这个空间

	public class SimpleDividerDecoration extends RecyclerView.ItemDecoration {
	
	    private int dividerHeight;
	    private Paint dividerPaint;
	
	    public SimpleDividerDecoration(Context context) {
	        dividerPaint = new Paint();
	        dividerPaint.setColor(context.getResources().getColor(R.color.colorAccent));
	        dividerHeight = context.getResources().getDimensionPixelSize(R.dimen.divider_height);
	    }
	
	
	    @Override
	    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
	        super.getItemOffsets(outRect, view, parent, state);
	        outRect.bottom = dividerHeight;
	    }
	
	    @Override
	    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
	        int childCount = parent.getChildCount();
	        int left = parent.getPaddingLeft();
	        int right = parent.getWidth() - parent.getPaddingRight();
	
	        for (int i = 0; i < childCount - 1; i++) {
	            View view = parent.getChildAt(i);
	            float top = view.getBottom();
	            float bottom = view.getBottom() + dividerHeight;
	            c.drawRect(left, top, right, bottom, dividerPaint);
	        }
	    }
	}

实现效果：


![Divider ItemDecoration.png](http://upload-images.jianshu.io/upload_images/186157-11a6344281993a69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#标签

现在很多电商app会给商品加上一个标签，比如“推荐”，“热卖”，“秒杀”等等，可以看到这些标签都是覆盖在内容之上的，这就可以用onDrawOver()来实现，我们这里简单实现一个有趣的标签

	public class LeftAndRightTagDecoration extends RecyclerView.ItemDecoration {
	    private int tagWidth;
	    private Paint leftPaint;
	    private Paint rightPaint;
	
	    public LeftAndRightTagDecoration(Context context) {
	        leftPaint = new Paint();
	        leftPaint.setColor(context.getResources().getColor(R.color.colorAccent));
	        rightPaint = new Paint();
	        rightPaint.setColor(context.getResources().getColor(R.color.colorPrimary));
	        tagWidth = context.getResources().getDimensionPixelSize(R.dimen.tag_width);
	    }
	
	    @Override
	    public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
	        super.onDrawOver(c, parent, state);
	        int childCount = parent.getChildCount();
	        for (int i = 0; i < childCount; i++) {
	            View child = parent.getChildAt(i);
	            int pos = parent.getChildAdapterPosition(child);
	            boolean isLeft = pos % 2 == 0;
	            if (isLeft) {
	                float left = child.getLeft();
	                float right = left + tagWidth;
	                float top = child.getTop();
	                float bottom = child.getBottom();
	                c.drawRect(left, top, right, bottom, leftPaint);
	            } else {
	                float right = child.getRight();
	                float left = right - tagWidth;
	                float top = child.getTop();
	                float bottom = child.getBottom();
	                c.drawRect(left, top, right, bottom, rightPaint);
	
	            }
	        }
	    }
	}

实现效果：

![Tag  ItemDecoration](http://upload-images.jianshu.io/upload_images/186157-252aa2ed0a0ef103.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#组合
不要忘记的是ItemDecoration是可以叠加的

 	recyclerView.addItemDecoration(new LeftAndRightTagDecoration(this));
    recyclerView.addItemDecoration(new SimpleDividerDecoration(this));

我们把上面2个ItemDecoration同时添加到RecyclerView看下什么效果

![ItemDecoration](http://upload-images.jianshu.io/upload_images/186157-00d3652a11399844.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

是不是有种狂拽炫酷吊炸天的赶脚。。。

三个方法都用了一遍，你以为这就结束了？呵呵 并没有

#section

这个是什么呢，先看下我们实现的效果


![Section ItemDecoration](http://upload-images.jianshu.io/upload_images/186157-0339858aeebc23ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一看这个就很熟悉吧，手机上面的通讯录联系人，知乎日报都是这样效果，可以叫分组，也可以叫section分块 先不管它叫什么。

这个怎么实现呢？ 其实和实现分割线是一样的道理 ，只是不是所有的item都需要分割线，只有同组的第一个需要。

我们首先定义一个接口给activity进行回调用来进行数据分组和获取首字母

	public interface DecorationCallback {
	
	        long getGroupId(int position);
	
	        String getGroupFirstLine(int position);
	    }

然后再来看我们的ItemDecoration

	public class SectionDecoration extends RecyclerView.ItemDecoration {
	    private static final String TAG = "SectionDecoration";
	
	    private DecorationCallback callback;
	    private TextPaint textPaint;
	    private Paint paint;
	    private int topGap;
	    private Paint.FontMetrics fontMetrics;
	
	
	    public SectionDecoration(Context context, DecorationCallback decorationCallback) {
	        Resources res = context.getResources();
	        this.callback = decorationCallback;
	
	        paint = new Paint();
	        paint.setColor(res.getColor(R.color.colorAccent));
	
	        textPaint = new TextPaint();
	        textPaint.setTypeface(Typeface.DEFAULT_BOLD);
	        textPaint.setAntiAlias(true);
	        textPaint.setTextSize(80);
	        textPaint.setColor(Color.BLACK);
	        textPaint.getFontMetrics(fontMetrics);
	        textPaint.setTextAlign(Paint.Align.LEFT);
	        fontMetrics = new Paint.FontMetrics();
	        topGap = res.getDimensionPixelSize(R.dimen.sectioned_top);//32dp
	
	
	    }
	
	
	    @Override
	    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
	        super.getItemOffsets(outRect, view, parent, state);
	        int pos = parent.getChildAdapterPosition(view);
	        Log.i(TAG, "getItemOffsets：" + pos);
	        long groupId = callback.getGroupId(pos);
	        if (groupId < 0) return;
	        if (pos == 0 || isFirstInGroup(pos)) {//同组的第一个才添加padding
	            outRect.top = topGap;
	        } else {
	            outRect.top = 0;
	        }
	    }
	
	    @Override
	    public void onDraw(Canvas c, RecyclerView parent, RecyclerView.State state) {
	        super.onDraw(c, parent, state);
	        int left = parent.getPaddingLeft();
	        int right = parent.getWidth() - parent.getPaddingRight();
	        int childCount = parent.getChildCount();
	        for (int i = 0; i < childCount; i++) {
	            View view = parent.getChildAt(i);
	            int position = parent.getChildAdapterPosition(view);
	            long groupId = callback.getGroupId(position);
	            if (groupId < 0) return;
	            String textLine = callback.getGroupFirstLine(position).toUpperCase();
	            if (position == 0 || isFirstInGroup(position)) {
	                float top = view.getTop() - topGap;
	                float bottom = view.getTop();
	                c.drawRect(left, top, right, bottom, paint);//绘制红色矩形
	                c.drawText(textLine, left, bottom, textPaint);//绘制文本
	            }
	        }
	    }
	
		
	    private boolean isFirstInGroup(int pos) {
	        if (pos == 0) {
	            return true;
	        } else {
	            long prevGroupId = callback.getGroupId(pos - 1);
	            long groupId = callback.getGroupId(pos);
	            return prevGroupId != groupId;
	        }
	    }
	
	    public interface DecorationCallback {
	
	        long getGroupId(int position);
	
	        String getGroupFirstLine(int position);
	    }
	}

可以看到和divider实现一样，都是重写getItemOffsets()和onDraw()2个方法，不同的是根据数据做了处理。

在Activity中使用

	 recyclerView.addItemDecoration(new SectionDecoration(this, new SectionDecoration.DecorationCallback() {
	            @Override
	            public long getGroupId(int position) {
	                return Character.toUpperCase(dataList.get(position).getName().charAt(0));
	            }
	
	            @Override
	            public String getGroupFirstLine(int position) {
	                return dataList.get(position).getName().substring(0, 1).toUpperCase();
	            }
	        }));

干净舒服，不少github类似的库都是去adapter进行处理 侵入性太强 或许ItemDecoration是个更好的选择，可插拔，可替换。

到这里细心的人就会发现了，header不会动啊，我手机上的通讯录可是会随的滑动而变动呢，这个可以实现么？

#StickyHeader

这个东西怎么叫我也不知道啊 粘性头部？英文也有叫 pinned section 取名字真是个麻烦事。

先看下我们简单实现的效果


![stickyheader](http://upload-images.jianshu.io/upload_images/186157-06489058260d58d5.gif?imageMogr2/auto-orient/strip)

首先一看到图，我们就应该想到header不动肯定是要绘制item内容之上的，需要重写onDrawOver()方法，其他地方和section实现一样。


	public class PinnedSectionDecoration extends RecyclerView.ItemDecoration {
	    private static final String TAG = "PinnedSectionDecoration";
	
	    private DecorationCallback callback;
	    private TextPaint textPaint;
	    private Paint paint;
	    private int topGap;
	    private Paint.FontMetrics fontMetrics;
	
	
	    public PinnedSectionDecoration(Context context, DecorationCallback decorationCallback) {
	        Resources res = context.getResources();
	        this.callback = decorationCallback;
	
	        paint = new Paint();
	        paint.setColor(res.getColor(R.color.colorAccent));
	
	        textPaint = new TextPaint();
	        textPaint.setTypeface(Typeface.DEFAULT_BOLD);
	        textPaint.setAntiAlias(true);
	        textPaint.setTextSize(80);
	        textPaint.setColor(Color.BLACK);
	        textPaint.getFontMetrics(fontMetrics);
	        textPaint.setTextAlign(Paint.Align.LEFT);
	        fontMetrics = new Paint.FontMetrics();
	        topGap = res.getDimensionPixelSize(R.dimen.sectioned_top);
	
	
	    }
	
	
	    @Override
	    public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
	        super.getItemOffsets(outRect, view, parent, state);
	        int pos = parent.getChildAdapterPosition(view);
	        long groupId = callback.getGroupId(pos);
	        if (groupId < 0) return;
	        if (pos == 0 || isFirstInGroup(pos)) {
	            outRect.top = topGap;
	        } else {
	            outRect.top = 0;
	        }
	    }
	
	
	    @Override
	    public void onDrawOver(Canvas c, RecyclerView parent, RecyclerView.State state) {
	        super.onDrawOver(c, parent, state);
	        int itemCount = state.getItemCount();
	        int childCount = parent.getChildCount();
	        int left = parent.getPaddingLeft();
	        int right = parent.getWidth() - parent.getPaddingRight();
	        float lineHeight = textPaint.getTextSize() + fontMetrics.descent;
	
	        long preGroupId, groupId = -1;
	        for (int i = 0; i < childCount; i++) {
	            View view = parent.getChildAt(i);
	            int position = parent.getChildAdapterPosition(view);
	
	            preGroupId = groupId;
	            groupId = callback.getGroupId(position);
	            if (groupId < 0 || groupId == preGroupId) continue;
	
	            String textLine = callback.getGroupFirstLine(position).toUpperCase();
	            if (TextUtils.isEmpty(textLine)) continue;
	
	            int viewBottom = view.getBottom();
	            float textY = Math.max(topGap, view.getTop());
	            if (position + 1 < itemCount) { //下一个和当前不一样移动当前
	                long nextGroupId = callback.getGroupId(position + 1);
	                if (nextGroupId != groupId && viewBottom < textY ) {//组内最后一个view进入了header
	                    textY = viewBottom;
	                }
	            }
	            c.drawRect(left, textY - topGap, right, textY, paint);
	            c.drawText(textLine, left, textY, textPaint);
	        }
	
	    }
	
	}

好了，现在发现ItemDecoration有多强大了吧！ 当然还有更多就需要你自己去发现了。
























