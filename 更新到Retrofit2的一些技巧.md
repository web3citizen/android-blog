>原文链接：[Tips on updating to Retrofit 2](http://www.fasteque.com/tips-on-updating-to-retrofit-2/)
>ps：其实我老早就更新到Retrofit2了  但是不同的beta版目前改变还是挺大的，如果你在用的话 ，要时刻跟进并查看它的changelog.该文章还是比较倾向于1.9升级2.0  但是就像前面说的 beta2到beta4改变也是非常大的 所以同样可以参考用于备忘

Retrofit2.0已经公开发布几个月了，但是现在依然是beta版，一些开发者已经开始从之前的版本进行升级，虽然官方已经提供了文档，文章或博客让你了解最新版的相关信息，但是在StackOverflow还是可以看到一些提问关于在升级版本的过程中怎么处理常见功能像logging打印日志，添加request parameter或者使用JSON对象等，所以在这篇文章中，我会告诉你关于这些问题的一些技巧。

写文章时，Retrofit的版本是v2.0.0-beta4.

###新的group id
第一个你会注意到的改变是新的group id,变成了**com.squareup.retrofit2**，所以新的依赖声明是：

    
    compile 'com.squareup.retrofit2:retrofit:2.0.0-beta4'

###新的包名

主包名已经改成了

    package retrofit2;

这意味的你需要更改所有关于Retrofit的import，此外，如果你还有其他涉及到包名的代码或任务，记得也要更新他们。

###proguard 配置

因为新包名，如果你开启了混淆代码，第一件要做的事就是更新配置文件的规则，它们可以在官方网站找到：

    -dontwarn retrofit2.**
    -keep class retrofit2.** { *; }
    -keepattributes Signature
    -keepattributes Exceptions

###okhttp

okhttp现在Retrofit2内置的默认依赖，实际上，查看Retrofit2的pom文件(maven的依赖配置文件)，会发现下面的依赖：

    <dependency>
          <groupId>com.squareup.okhttp3</groupId>
          <artifactId>okhttp</artifactId>
    </dependency>

如果你不需要用一个特殊版本的okhttp或者其他http client,你不用做任何事情。

###生成服务接口实现

>service interface implementation generation

你会立即注意到的另外一件事是你的生成服务接口实现的代码没法编译了。

下面是Retrofit的典型代码：

    RestAdapter restAdapter = new RestAdapter.Builder()
                    .setEndpoint("https://api.github.com")
                    .build();
    GitHubService service = restAdapter.create(GitHubService.class);

在Retrofit2，会变成：

    Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://api.github.com")
        .build();
    GitHubService service = retrofit.create(GitHubService.class);

你可以看到，依然是build模式，但是涉及的方法和类已经变了。

###logging

在开发中打印网络请求和返回结果是非常重要的，如果你在Retrofit中启用了这个功能，你会发现实现该功能的方法已经不可用了。

    setLogLevel(RestAdapter.LogLevel.FULL)    

那是因为你现在必须依靠okhttp提供的日志系统，HttpLoggingInterceptor.

首先声明一个新的依赖，因为它不是okhttp的一部分所以要另外添加依赖：

    compile 'com.squareup.okhttp3:logging-interceptor:3.1.2'

然后，创建一个interceptor实例：

    HttpLoggingInterceptor httpLoggingInterceptor = new HttpLoggingInterceptor();
    httpLoggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);

再然后，添加到OkHttp client实例中：
    
    OkHttpClient okHttpClient = new OkHttpClient.Builder().addInterceptor(httpLoggingInterceptor).build();

最后，把client设置到service interface实现中：

    Retrofit retrofit = new Retrofit.Builder()
        .client(okHttpClient)
        .baseUrl(MovieDbApi.END_POINT)
        .build();
    movieDbApi = retrofit.create(MovieDbApi.class);

ps:其实这个log并不是很好用，最好自己参考源码自己自定义一个，分分钟就搞定了。

###gson converter

>By default, Retrofit can only deserialize HTTP bodies into OkHttp’s ResponseBody type and it can only accept its RequestBodytype for @Body.

这意味得用来支持其他types的converter必须在创建服务接口时进行设置

为了让JSON和Java对象相互转换，我们需要设置一个GSON转换器

首先，需要一个新的依赖：

    compile 'com.squareup.retrofit2:converter-gson:2.0.0-beta4'

然后设置converter factory:

    Retrofit retrofit = new Retrofit.Builder()
        .client(okHttpClient)
        .baseUrl(MovieDbApi.END_POINT)
        .addConverterFactory(GsonConverterFactory.create())
        .build();
    movieDbApi = retrofit.create(MovieDbApi.class);

完整的可用converters可以查看[官方文档](http://square.github.io/retrofit/#restadapter-configuration)

###interceptor

Retrofit另外一个有用的功能是可以拦截http请求进行监控，重写或重试

一个典型应用场景是所有http请求需要加上api key,在Retrofit2之前，可以通过RequestInterceptor实现：

    final RequestInterceptor authorizationInterceptor = new RequestInterceptor() {
            @Override
            public void intercept(RequestInterceptor.RequestFacade request) {
                request.addQueryParam(MovieDbApi.PARAM_API_KEY, "YOUR_API_KEY");
            }
        };

然后 

    RestAdapter restAdapter = new RestAdapter.Builder()
                    .setEndpoint(MovieDbApi.END_POINT)
                    .setRequestInterceptor(authorizationInterceptor)
                    .build();
    movieDbApi = restAdapter.create(MovieDbApi.class);

在Retrofit2中已经不再有效了,因为你现在必须依靠OKHttp [interceptors](https://github.com/square/okhttp/wiki/Interceptors).

你可以直接在OKHttp client实例化时进行设置：

    OkHttpClient okHttpClient = new OkHttpClient.Builder().addInterceptor(new Interceptor() {
        @Override
        public Response intercept(Chain chain) throws IOException {
            Request request = chain.request();
            HttpUrl url = request.url().newBuilder().addQueryParameter(
    MovieDbApi.PARAM_API_KEY, BuildConfig.MOVIE_DB_API_KEY).build();
            request = request.newBuilder().url(url).build();
            return chain.proceed(request);
        }
    }).build();

然后像前面那样设置client：

    Retrofit retrofit = new Retrofit.Builder()
        .client(okHttpClient)
        .baseUrl(MovieDbApi.END_POINT)
        .build();
    movieDbApi = retrofit.create(MovieDbApi.class);

###RxJava observable

如果你正在使用RxJava,你会注意到Retrofit2 interfaces已经不支持Observable了，实际上，Call模式被用于标准的http请求。

当然你可以用你自己的类型，提供你自己的CallAdapter实现，但是幸运的是已经有可用的了，RxJavaCallAdapterFactory.简单说，它把Call转换成Observable.

首先，添加依赖：

    com.squareup.retrofit2:adapter-rxjava:2.0.0-beta4

然后，用addCallAdapterFactory进行设置：

    Retrofit retrofit = new Retrofit.Builder()
        .client(okHttpClient)
        .baseUrl(MovieDbApi.END_POINT)
        .addConverterFactory(GsonConverterFactory.create())
        .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
        .build();
    movieDbApi = retrofit.create(MovieDbApi.class);