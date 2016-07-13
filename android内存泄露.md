>原文链接：[How to make your own File Templates in Android Studio](http://riggaroo.co.za/custom-file-templates-android-studio/)

>文章的GitHub地址：[android内存泄露](https://github.com/thinkSky1206/android-blog/blob/master/Android%20Studio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%96%87%E4%BB%B6%E6%A8%A1%E6%9D%BF.md)

>译者注：之前看到内存泄露标题的文章都是一扫而过，私以为要么华而不实，要么晦涩难懂，最近自己学习实践了一下，算是有所收获了吧。


本文主要是总结一下自己项目实践过程中遇到的情况。

如何发现和解决大家去阅读下面相关文章，基本都给出了答案了。


- [Memory leaks in Android](https://medium.com/freenet-engineering/memory-leaks-in-android-identify-treat-and-avoid-d0b1233acc8#.43c1t9l8w)
-  [常见的八种导致 APP 内存泄漏的问题](http://blog.nimbledroid.com/2016/05/23/memory-leaks-zh.html)
-  [基于Android Studio的内存泄漏检测与解决全攻略](http://wetest.qq.com/lab/view/?id=99)


#1.监听器

当你使用系统服务SensorManager,LocationManager的时候，你需要注册监听器来被通知当它们获取信息的时候，这样它们就持有了activity的引用，当你销毁activiy的时候没有取消监听器的话，就会导致泄露了

解决方法：onDestroy的时候取消注册监听器

#2.静态变量引用context

这种情况很常见的就是：单例持有activity的context

因为单例的生命周期比activity的长，所以activity一直被引用没法回收，导致泄露。

例如：有人会用ActivitieManager之类的用List保存activity来处理退出app,页面跳转操作，你会发现占用内存会一直居高不下。

解决方法：生命周期短的不要引用生命周期长的

1. activity context替换成application context
2. 静态变量避免引用activity context


#3.内部类

内部类用的地方非常多，因为可以增加封装性和可读性，使用也很方便。不幸的是内部类能够访问外部类这一优势就是通过持有外部类的引用来实现的。

例如：activity里面的各种adapter

解决方法：

1. 声明成静态内部类
2. 如果要访问外部类组件，声明成WeakReference

#4.匿名内部类

这个情况就很多了， 比如AsyncTask，Handlers，Threads，Timer Tasks

匿名内部类和内部类的情况差不多都是默认持有外部类的强引用，注意：大部分匿名内部类都用于异步执行，执行完成更新UI的时候不要忘了判断VIEW是否为空。

例如：Retrofit的CallBack

解决方法：和内部类一样

贴个上面文章提到的AsyncTask小例子看一下就一目了然：

**修改之前**


	public class AsyncActivity extends Activity {
	
	    TextView textView;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_async);
	        textView = (TextView) findViewById(R.id.textView);
	
	        new BackgroundTask().execute();
	    }
	
	    private class BackgroundTask extends AsyncTask<Void, Void, String> {
	
	        @Override
	        protected String doInBackground(Void... params) {
	            // Do background work. Code omitted.
	            return "some string";
	        }
	
	        @Override
	        protected void onPostExecute(String result) {
	            textView.setText(result);
	        }
	    }
	}



**修改之后**

	
	public class AsyncActivity extends Activity {
	
	    TextView textView;
	    AsyncTask task;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_async);
	        textView = (TextView) findViewById(R.id.textView);
	
	        task = new BackgroundTask(textView).execute();
	    }
	    
	    @Override
	    protected void onDestroy() {
	        task.cancel(true);
	        super.onDestroy();
	    }
	
	    private static class BackgroundTask extends AsyncTask<Void, Void, String> {
	
	        private final WeakReference<TextView> textViewReference;
	
	        public BackgroundTask(TextView resultTextView) {
	            this.textViewReference = new WeakReference<>(resultTextView);
	        }
	        
	        @Override
	        protected void onCancelled() {
	            // Cancel task. Code omitted.
	        }
	
	        @Override
	        protected String doInBackground(Void... params) {
	            // Do background work. Code omitted.
	            return "some string";
	        }
	
	        @Override
	        protected void onPostExecute(String result) {
	            TextView view = textViewReference.get();
	            if (view != null) {
	                view.setText(result);
	            }
	        }
	    }
	}