最近和Gradle打交道的时间挺多的，很多在构建打包过程中的不少奇奇怪怪的需求都用gradle解决了，给开发过程节省了不少时间，也让我对gradle刮目相看啊，看来得找时间得好好深入了解下。

好了，废话不多说了，看下我们的需求。

app是针对海外开发的，所以在开发的时候一般都是基于本地后台服务接口开发，到了某个时间或某个版本的时候，再把后台服务接口更换成海外服务接口打包给本地测试同事或海外测试同事进行安装测试。本来这也没太问题，你要哪个版本测试我就给你改下服务接口地址重新打个包就好了。

假设下面是我们的目前的代码，想要哪个地址就改下API_URL重新打个包就ok了

    //public static final String API_URL = "http://www.google.com";//美国us
    //public static final String API_URL = "http://www.nanfei.com";//南非za
    public static final String API_URL = "http://www.baidu.com";//本地local

    Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(API_URL)
                .client(client)
                .addConverterFactory(GsonConverterFactory.create())
                .build();


可是有一天，测试同事跑来说，每次测试不同版本只能安装一个app(applicationId是唯一的,会进行覆盖),他想在同一台手机上安装多个app，app之间的区别只是它们的后台服务接口地址API_URL不同，当然最好app的桌面名字能区分出来是哪个服务接口地址,这样测试就方便了

如何实现呢，同时打多个包肯定会想到用productFlavors，同时API_URL和app\_name需要动态改变。

##1.移除strings的app_name
由于app\_name是动态的所以肯定不能写死了,把它删掉

    <!-- <string name="app_name">app</string>-->


##2.设置build.gradle的productFlavors

    productFlavors {
        local {
            applicationId "com.lwp.app"
            buildConfigField 'String', 'API_URL', '"http://www.baidu.com"'
            resValue "string", "app_name", "app"
        }
        us {
            applicationId "com.lwp.app.us"
            buildConfigField 'String', 'API_URL', '"http://www.google.com"'
            resValue "string", "app_name", "app_us"

        }
        za {
            applicationId "com.lwp.app.za"
            buildConfigField 'String', 'API_URL', '"http://www.nanfei.com"'
            resValue "string", "app_name", "app_za"
        }
    }

上面我们分别设置三个版本各自的applicationId,API_URL,app\_name

##3.代码中引用动态API_URL
我们设置了不同版本的对应API\_URL,代码肯定是要用它的，build完毕后。

直接可以用**BuildConfig.API\_URL**，这个是动态的，不同版本会自动生成不同的值。

    Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(BuildConfig.API_URL)
                .client(client)
                .addConverterFactory(GsonConverterFactory.create())
                .build();


##4.执行gradlew assembleDebug

执行命令后会生成类似

    app_local_debug.apk
    app_us_debug.apk
    app_za_debug.apk

安装完成后，桌面会显示app,app\_us,app\_za三个图标一样的app

他们功能完全一样，只是后台服务接口地址API\_URL不一样。

这样就可以愉快的同时进行三个不同版本app测试同时互相比对数据的正确性了。