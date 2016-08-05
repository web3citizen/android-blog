
>文章的GitHub地址：[Drawable Resources一览](https://github.com/thinkSky1206/android-blog/blob/master/android%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2.md)

>译者注：之前某个国外开发者大会释放出来的，整理一下记录下来，看看你都会么？


#Bitmap File

![bitmap](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_bitmap.png)

#Nine-Path File

![.9 图片](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_nine.png)

#Layer List

	<?xml version="1.0" encoding="utf-8"?>
	<layer-list xmlns:android="http://schemas.android....
		<item
			android:width="40dp"
			android:height="40dp"
			android:drawable="@drawable/ic_activation_01"
			android:gravity="center" />
		<item
			android:width="30dp"
			android:height="30dp"
			android:drawable="@drawable/ic_activation_02"
			android:gravity="center" />
		<item
			android:width="20dp"
			android:height="20dp"
			android:drawable="@drawable/ic_activation_03"
			android:gravity="center" />
	</layer-list>

![layer list](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_layer.png)

#State List

	<?xml version="1.0" encoding="utf-8"?>
	<selector xmlns:android="http://schemas.android....
		<item
			android:drawable="@drawable/btn_ok_pressed"
			android:state_enabled="true"
			android:state_pressed="true" />
		<item
			android:drawable="@drawable/btn_ok_disable"
			android:state_enabled="false" />
		<item
			android:drawable="@drawable/btn_ok_normal" />
	</selector>

![state list](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_state.png)

#Level List

要实现一个多状态显示的效果，像下面这样写？

	ImageView ivScore = (ImageView)findViewById(R.id.iv_asd);
	int score = ...;
	if(score == 0) {
		ivScore.setImageResource(R.drawable.ic_score_bad);
	} else if(score == 1) {
		ivScore.setImageResource(R.drawable.ic_score_ok);
	} else if(score == 2) {
		ivScore.setImageResource(R.drawable.ic_score_good);
	}
![level list](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_level.png)

No 下面才是优雅的方式


	<?xml version="1.0" encoding="utf-8"?>
	<level-list xmlns:android="http://schemas.android...
		<item
			android:drawable="@drawable/ic_score_bad"
			android:maxLevel="0" />
		<item
			android:drawable="@drawable/ic_score_ok"
			android:maxLevel="1" />
		<item
			android:drawable="@drawable/ic_score_good"
			android:maxLevel="2" />
	</level-list>


	ImageView ivScore = (ImageView)findViewById(R.id.iv_score);
	int score = ...;
	ivScore.setImageLevel(score);

#Transition Drawable
>用来设置2个图片的渐变效果

	<?xml version="1.0" encoding="utf-8"?>
	<transition xmlns:android="http://schemas.android.com/...
		<item
			android:drawable="@drawable/ic_score_very_bad" />
		<item
			android:drawable="@drawable/ic_score_very_good" />
	</transition>

	ImageView ivScore = (ImageView) findViewById(R.id.iv_score);
	TransitionDrawable drawable =(TransitionDrawable) ivScore.getDrawable();
	drawable.startTransition(1000);

![transition](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_transition.png)


	drawable.reverseTransition(1000);

![transition2](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_transition2.png)

#Inset Drawable

	<?xml version="1.0" encoding="utf-8"?>
	<inset xmlns:android="http://schemas.android.com/...
		android:drawable="@drawable/bg_badge"
		android:insetBottom="10dp"
		android:insetLeft="10dp"
		android:insetRight="10dp"
		android:insetTop="10dp"/>

![inset](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_inset.png)

#Clip Drawable
>图片裁剪，可以实现很多很好玩的效果，裁剪范围0-10000，5000也就是裁剪一半


	<?xml version="1.0" encoding="utf-8"?>
	<clip xmlns:android="http://schemas.android.com/...
		android:clipOrientation="horizontal"
		android:drawable="@drawable/ic_rate_very_good"
		android:gravity="left" />

	ImageView ivRate = (ImageView) findViewById(R.id.iv_rate);
	ClipDrawable drawable = (ClipDrawable) ivRate.getDrawable();
	...
	drawable.setLevel(5000);

![clip](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_clip.png)

#Scale Drawable

	<?xml version="1.0" encoding="utf-8"?>
	<scale xmlns:android="http://schemas.android.com/...
		android:drawable="@drawable/ic_code_mania"
		android:scaleGravity="center"
		android:scaleHeight="60%"
		android:scaleWidth="60%" />

![scale](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_scale.png)


#Shape Drawable
  
	<?xml version="1.0" encoding="utf-8"?>
	<shape xmlns:android="http://schemas.android.com/...
		<stroke
			android:width="4dp"
			android:color="#ffffff"
			android:dashGap="10dp"
			android:dashWidth="10dp" />
		<solid
			android:color="#237793" />
		<corners
			android:radius="10dp" />
	</shape>


![shape](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_shape.png)


#Drawable Mixing 
>结合使用
	
	<?xml version="1.0" encoding="utf-8"?>
	<clip xmlns:android="http://schemas.android.com/apk/res/android"
		android:clipOrientation="horizontal"
		android:gravity="left">
		<shape>
			<solid
				android:color="#ffd200" />
			<corners
				android:radius="50dp" />
		</shape>
	</clip>

![mix](https://github.com/thinkSky1206/android-blog/blob/master/images/drawable_mix.png)
