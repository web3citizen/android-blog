>原文链接：[Android Shared-Element Transitions for all](https://medium.com/@aitorvs/android-shared-element-transitions-for-all-b90e9361507d#.uvdaet42o)

>译文的GitHub地址：[Android Studio自定义文件模板](https://github.com/thinkSky1206/android-blog/blob/master/Android%20Studio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%96%87%E4%BB%B6%E6%A8%A1%E6%9D%BF.md)

>译者注：

![image](http://i1.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/androidstudiofiletemplates.png)

我们都想要我们的app通过material设计更加个性化，activity切换动画是一个让用户记住我们app的好方法。在切换动画中，共享元素动画可能是最常用的。


元素共享在Lollipop+很容易实现，但是如果你想应用在老版本上，有点麻烦，你要开始检查android版本并实现旧版本动画转换。。。或者 你够勇敢 手动实现动画兼容所有设备

听起来很可怕？好吧，我会告诉你没那么复杂。

#主要步骤

假设你想从Activity A->Activity B并且他们共享一个元素。在这个场景下，用户最好的体验是过渡到ActivityB,这样可以保持用户关注在重要点上
怎样达到这样的过渡动画呢？下面是几个重要的步骤。

1. Activity A捕捉共享元素的初始值，然后通过Intent传递给Activity B
2. Activity B初始设成全透明
3. Activity B读取传递过来的值并准备场景
4. Activity B开始执行共享元素动画

下面我会详解这些步骤并给出示范代码。

首先，我们把Activity A的共享view称为origin view,Activity的共享view叫destination view,记住，这两个view，虽然它们被称为共享，但它们实际上是两个独立的视图对象，只不过内容相同。

让我们开始吧。

#Activity A-捕捉和传递初始值



	Intent intent = new Intent(context, ArticleImageActivity.class);
    intent.putExtra(IMAGE_URL_EXTRA, imageUrl);
    intent.putExtra(VIEW_INFO_EXTRA, /* start values */ captureValues(originView));

    startActivity(intent);
    overridePendingTransition(0, 0);

要记住调用overridePendingTransition(0, 0)禁用默认的过渡动画。


方法captureValues主要返回view的位置和size


	 private Bundle captureValues(@NonNull View view) {
	        Bundle b = new Bundle();
	        int[] screenLocation = new int[2];
	        view.getLocationOnScreen(screenLocation);
	        b.putInt(PROPNAME_SCREENLOCATION_LEFT, screenLocation[0]);
	        b.putInt(PROPNAME_SCREENLOCATION_TOP, screenLocation[1]);
	        b.putInt(PROPNAME_WIDTH, view.getWidth());
	        b.putInt(PROPNAME_HEIGHT, view.getHeight());
	        return b;
	    }

#Activity B-初始设成透明

	<style name="Transparent" parent="AppTheme">
        <item name="android:windowIsTranslucent">true</item>
        <item name="android:windowBackground">@android:color/transparent</item>
    </style>


#Activity B-准备场景

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_article_image);
        ...
        // start the information passed in the Bundle
        extractViewInfoFromBundle(getIntent());
        // only now we load the image
        Picasso.load(mImageUrl)
                .into(mDestinationView, new Callback() {
                    @Override
                    public void onSuccess() {
                        // we've got the image loaded, we can start prepping the scene
                        onUiReady();
                    }
                    @Override
                    public void onError() {...}
                });
        ...
    }


	  private void onUiReady() {
	        mDestinationView.getViewTreeObserver().addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() {
	            @Override
	            public boolean onPreDraw() {
	                // remove previous listener
	                mDestinationView.getViewTreeObserver().removeOnPreDrawListener(this);
	                // prep the scene
	                prepareScene();
	                // run the animation
	                runEnterAnimation();
	                return true;
	            }
	        });
	    }

	 private void prepareScene() {
	        // capture the end values in the destionation view
	        mEndValues = captureValues(mDestinationView);
	
	        // calculate the scale and positoin deltas
	        float scaleX = scaleDelta(mStartValues, mEndValues);
	        float scaleY = scaleDelta(mStartValues, mEndValues);
	        int deltaX = translationDelta(mStartValues, mEndValues);
	        int deltaY = translationDelta(mStartValues, mEndValues);
	
	        // scale and reposition the image
	        mDestinationView.setScaleX(scaleX);
	        mDestinationView.setScaleY(scaleY);
	        mDestinationView.setTranslationX(deltaX);
	        mDestinationView.setTranslationY(deltaY);
	    }


#Activity B — 执行动画


	 private void runEnterAnimation() {
	        // We can now make it visible
	        mDestinationView.setVisibility(View.VISIBLE);
	        // finally, run the animation
	        mDestinationView.animate()
	                .setDuration(DEFAULT_DURATION)
	                .setInterpolator(DEFAULT_INTERPOLATOR)
	                .scaleX(1f)
	                .scaleY(1f)
	                .translationX(0)
	                .translationY(0)
	                .start();
	    }



	 private void runExitAnimation() {
	        mDestinationView.animate()
	                .setDuration(DEFAULT_DURATION)
	                .setInterpolator(DEFAULT_INTERPOLATOR)
	                .scaleX(scaleX)
	                .scaleY(scaleY)
	                .translationX(deltaX)
	                .translationY(deltaY)
	                .withEndAction(new Runnable() {
	                    @Override
	                    public void run() {
	                        finish();
	                        overridePendingTransition(0, 0);
	                    }
	                }).start();
	    }