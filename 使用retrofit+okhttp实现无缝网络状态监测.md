>原文：[SEAMLESS NETWORK STATE MONITORING WITH RETROFIT + OKHTTP](http://blog.stablekernel.com/seamless-network-state-monitoring-with-retrofit-okhttp?utm_campaign=stable%7Ckernel+Developer+Blog+&utm_source=hs_email&utm_medium=email&utm_content=34158359&_hsenc=p2ANqtz-93ZBGz1S-0ajswYTtZ7-00zxDWnSw-juNh8FebmXWTnvf4CxLxAM4AuQpjSN7b47_8Glc4F4JxY1Q6C0J0Xb_n0mff-g&_hsmi=34158359)

>译文的GitHub地址：[使用retrofit+okhttp实现无缝网络状态监测](https://github.com/thinkSky1206/android-blog/blob/master/Android%20Studio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%96%87%E4%BB%B6%E6%A8%A1%E6%9D%BF.md)

>译者注：我得去补补Application Interceptor和Network Interceptor 

在依赖web服务的android app中，我们通常要在发送web请求前检查网络的状态。这样我们就可以弹出提示告诉用户问题而不是等待网络请求超时。网络状态通常需要调用ConnectivityManager。然而每个请求都要这样做有点繁琐，现在最流行的网络请求解决方案是使用Retrofit。当使用Retrofit时处理异步结果最简单的方式是用RxJava Observables。如果你正在使用Retrofit和RxJava 那么就有一种简单的方式来实现监测网络连接状态。让我们来看看吧！

首先我们创建一个监测网络接口，这样我们就可以使用依赖注入在测试的时候进行切换：


	public interface NetworkMonitor {
	    boolean isConnected();
	}


然后实现这个接口并调用系统的ConnectivityManager。


	public class LiveNetworkMonitor implements NetworkMonitor {
	
	    private final Context applicationContext;
	
	    public LiveNetworkMonitor(Context context) {
	        applicationContext = context.getApplicationContext();
	    }
	
	    public boolean isConnected() {
	        ConnectivityManager cm =
	                (ConnectivityManager) applicationContext.getSystemService(Context.CONNECTIVITY_SERVICE);
	
	        NetworkInfo activeNetwork = cm.getActiveNetworkInfo();
	        return activeNetwork != null &&
	                activeNetwork.isConnectedOrConnecting();
	    }
	}

假设你已经配置好了Retrofit,在我们这个例子中我们打算用github api获取public events，如下：


	public interface GithubWebService {
	
	    @GET("events")
	    Observable<List<Event>> getPublicEvents();
	}

然后在我们的activity中调用这个请求接口：


	public class MainActivity extends AppCompatActivity {
	
	
	    protected GithubWebService githubWebService = ...;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        githubWebService.getPublicEvents()
	                .observeOn(AndroidSchedulers.mainThread())
	                .subscribe(events -> {
	                    // TODO onNext
	                }, throwable -> {
	                    // TODO onError
	                });
	    }
	}

额 那我们在哪里调用这个方法NetworkMonitor.isConnected() 才能实现每次网络请求进行无缝网络检查呢？让我们看一下创建Retrofit对象的dagger module代码：

	@Provides
	@Singleton
	GithubWebService provideWebService() {
	
	    String baseUrl = "https://api.github.com";
	
	    Retrofit.Builder builder = new Retrofit.Builder()
	            .baseUrl(baseUrl)
	            .addConverterFactory(GsonConverterFactory.create())
	            .addCallAdapterFactory(RxJavaCallAdapterFactory
	                                    .createWithScheduler(Schedulers.io()));
	
	    OkHttpClient.Builder okHttpClientBuilder = new OkHttpClient.Builder();
	
	    return builder.client(okHttpClientBuilder.build())
	            .build()
	            .create(GithubWebService.class);
	}

为了监测网络状态我们将使用OkHttp Interceptor! 拦截器可以让我们拦截网络请求并在把请求交给okhttp管道前做一些我们自己工作，我们可以监测网络是否可连接然后决定继续请求还是抛出一个自定义RunTimeError异常。我们更新一下代码  provide方法参数并添加一个拦截器：
	
	@Provides
	@Singleton
	GithubWebService provideWebService(NetworkMonitor networkMonitor) {
	
	    String baseUrl = "https://api.github.com";
	
	    Retrofit.Builder builder = new Retrofit.Builder()
	            .baseUrl(baseUrl)
	            .addConverterFactory(GsonConverterFactory.create())
	            .addCallAdapterFactory(RxJavaCallAdapterFactory.createWithScheduler(Schedulers.io()));
	
	    OkHttpClient.Builder okHttpClientBuilder = new OkHttpClient.Builder();
	
	    // 看这里 ！！！ 我们添加了一个网络监听拦截器
	    okHttpClientBuilder.addInterceptor(chain -> {
	        boolean connected = networkMonitor.isConnected();
	        if (networkMonitor.isConnected()) {
	            return chain.proceed(chain.request());
	        } else {
	            throw new NoNetworkException();
	        }
	    });
	
	    return builder.client(okHttpClientBuilder.build())
	            .build()
	            .create(GithubWebService.class);
	}

我们在拦截器链中添加了我们的拦截器。这样在okhttp其他chain完成前会进行网络检查。我们要么继续处理这个chain要么抛出自定义 NoNetworkException.

我们最后要做的事（并且不幸的是每个地方都要这样做）是在onError方法中捕获这个异常：


	githubWebService.getPublicEvents()
	        .subscribeOn(AndroidSchedulers.mainThread()
	        .subscribe(events -> {
	            // TODO onNext
	        }, throwable -> {
	            // on Error
	            if (throwable instanceof NoNetworkException) {
	                // TODO handle 'no network'
	            } else {
	                // TODO handle some other error
	            }
	        });

在这个例子中，我决定使用的是Application Interceptor.我会把这个决定留给你是使用Application Interceptor还是Network Interceptor。如果你要Network Interceptor你只需要用 addNetworkInterceptor 替换 addInterceptor。

用 Application Interceptor，你不需要担心每个重定向和中间响应的网络检查。然而，当okhttp从缓存中检索到你的请求时依然会执行没必要的网络检查，这让缓存的作用大打折扣。

用 Network Interceptor,则相反：okhttp提供缓存响应时不会进行网络检查。然而,你却需要为重定向和重试进行网络检查（译者:没理解好望指正  原文：you'll have the network check firing for redirects and retries），这有点矫枉过正。


最后例子代码  [android-okhttp-network-monitor](https://github.com/tir38/android-okhttp-network-monitor)

