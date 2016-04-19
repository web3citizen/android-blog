  在开发过程中经常会遇到debug/release版本中某个值需要动态改变方便开发和测试，就像BuildConfig的DEBUG一样，在debug版本中为true,release版本中为false,这样不用我们手动每次去修改，在开发过程中还是比较方便的。

 最近的工作中由于使用到了百度地图SDK，使用过百度地图SDK的人可能知道百度给我的Key是根据我们的秘钥sha1和包名生成的，所以这样就产生了一个问题，当我们打包debug和release包时需要不同的key,或者使用gradle productFlavors修改applicationId同时打多个不同版本的包时，每个版本包的key是不同的，这就需要我们动态设置key

所以我们的需求是
    
    debug:key=1234
    release:key=56789

这样我们就不用每次修改Key再去打包了，那如何实现呢？

##1.在AndroidManifest.xml设置占位
    <meta-data
            android:name="com.baidu.lbsapi.API_KEY"
            android:value="${baiduMapKey}"/>

${baiduMapKey}  baiduMapKey这个值是动态的,需要我们设置 

##2.在build.gradle中设置Debug/Release的baiduMapKey


    buildTypes {
        debug {
            manifestPlaceholders = [baiduMapKey: "1234"]
        }

        release {
            manifestPlaceholders = [baiduMapKey: "5678"]
       
        }
    }

##3.执行gradlew assembleDebug/assembleRelease
这个时候的debug版本key=1234
release版本key=5678

####就这么简单的完事了，是不是很简单方便