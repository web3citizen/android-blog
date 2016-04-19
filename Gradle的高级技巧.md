>原文：[Gradle, the Applidium way](http://applidium.com/en/news/gradle_tips/)

让我们继续谈论android生态中的Gradle

![gradle](http://upload-images.jianshu.io/upload_images/186157-65031426d1340c36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Gradle是Android studio用到的一个自动构建系统，基于Groovy语法，用来管理和构建Android项目，它可以精细的处理构建过程的各个步骤和简化持续集成（CI）,接下来让我们看看Gradle中一些有用的技巧。

#1.The dependency, mother of all dependencies

开始之前，这里有一个重要的工具可以让你处理依赖更简单方便：SDK Manager Plugin,它会自动下载和更新你的一些依赖（例如：API,support library,emulator）,非常好用，尤其是当你下载了别人的代码项目时，什么也干不了，这时只需打开项目，等待下载或更新依赖，然后你可以启动应用程序了！

使用这个插件，只需要在你Project的根目录的build.gradle加上一行就可以了

     dependencies {
       classpath 'com.android.tools.build:gradle:1.3.0'
       classpath 'com.jakewharton.sdkmanager:gradle-plugin:0.12.0' //添加该行
    }

更多该插件相关信息 [github](https://github.com/JakeWharton/sdk-manager-plugin)

#2.Variants opportunities

#####productFlavor and buildConfigField

另一个Gradle有用的功能是productFlavor,你可以在你的build.gradle定义不同的productFlavor，这将会生成不同版本的应用程序包

一个简单例子：我想生成2个不同分支的包

	productFlavors {
	    branchOne {
	        applicationId "com.example.branchOne"
	        buildConfigField "String", "CONFIG_ENDPOINT", "http://branchOne.com/android"
	   }
	    branchTwo {
	        applicationId "com.example.branchTwo"
	        buildConfigField "String", "CONFIG_ENDPOINT", "http://branchTwo.org"
	   }
	}

你可以在variants里面定义buildConfigFields，有了这些字段，这样你就可以让你的整个配置都写在一个文件里面。可以很容易的用来定义我们的常量（而不是分开写在几个xml文件），例如，我们刚才就它来定义了http请求的根url.

你可以在代码中使用它们：BuildConfig.FIELD_NAME（上面的的例子就是BuildConfig.CONFIG\_ENDPOINT）

一个有用的技巧：你可以单独只为某一个variants添加一些依赖，只需要在Compile加上对应的variant名字前缀就可以了

	dependencies {
	    compile 'com.android.support:support-v4:22.2.0'
	    branchOneCompile 'com.android.support:appcompat-v7:22.2.0'//只为branchOne添加这个依赖
	}


####buildType

和productFlavors类似的，还有buildTypes(debug和release是默认的)，它们都也可以为你的应用程序生成variants，合并一起形成buildVariant，brancheOne和debug会生成branchOneDebug.

差别在于改变buildType不会改变应用程序的代码，它们只是处理的东西不同，你可以通过buildType来获取更多的技术细节（例如：build optimization,log level等等），但是app的内容不会改变，相反的，使用productFlavor配置可以改变app的内容(ps:内容可以设想成package理解，buildType没法改applicationId)。

####flavorDimension

更进一步如果我们想基于多个标准构建多个版本，可以用flavorDimensions,看下面的例子：

	flavorDimensions "brand", "environment"
	productFlavors {
	    branchOne {
	        flavorDimension "brand"
	        applicationId "com.example.branchOne"
	        buildConfigField "String", "CONFIG_ENDPOINT", "http://branchOne.com/android"
	    }
	    branchTwo {
	        flavorDimension "brand"
	        applicationId "com.example.branchTwo"
	        buildConfigField "String", "CONFIG_ENDPOINT", "http://branchTwo.org"
	    }
	    stubs {
	        flavorDimension "environment"
	    }
	    preprod {
	        flavorDimension "environment"
	    }
	    distrib {
	        flavorDimension "environment"
	    }
	}

生成的variants（2个buildType\*2个brand维度\*3个environment维度=12）:

![variants](http://upload-images.jianshu.io/upload_images/186157-40c4893bd06b88a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过上面修改applicationId，每个版本的app都可以在你的手机上面安装共存。

####A dynamic Manifest

得感谢${name}，使得你可以在你的Manifest插入一个占位符。看下面的例子:

	<activity android:name=".Main">
	    <intent-filter>
	        <action android:name="${applicationId}.foo">
	        </action>
	    </intent-filter>
	</activity>

通过上面的代码，${applicationId}会被替换成真实的applicationId,例如对于branchOne这个variant,它会变成：

	<action android:name="com.example.branchOne.foo">

这是非常有用的，因为我们要根据variant用不同的applicationId填充Manifest.

如果你想创建自己的占位符，你可以在manifestPlaceholders定义，语法是：

	productFlavors {
	    branchOne {
	        manifestPlaceholders = [branchCustoName :"defaultName"]
	    }
	    branchTwo {
	        manifestPlaceholders = [branchCustoName :"otherName"]
	    }
	}

####To go further

如果你想做更复杂的事情，你可以applicationVariants.all这个task中添加代码进行执行。

思考一下，假设，我想设置一个特定的applicationId给branchTwo和distrib结合的variant,我可以在build.gradle里面这样写：

	applicationVariants.all { variant ->
	    def mergedFlavor = variant.mergedFlavor
	    switch (variant.flavorName) {
	        case "brancheTwoDistrib":
	            mergedFlavor.setApplicationId("com.example.oldNameBranchTwo")
	            break
	    }
	}

有时某些buildTypes-flavor结合没有意义，我们想告诉Gradle不要生成这些variants,没有问题，只需要用variant filter就可以做到

	variantFilter { variant ->
	    if (variant.buildType.name.equals('release')) {
	        variant.setIgnore(!variant.getFlavors().get(1).name.equals('distrib'));
	    }
	    if (variant.buildType.name.equals('debug')) {
	        variant.setIgnore(variant.getFlavors().get(1).name.equals('distrib'));
	    }
	}

在上面的代码中，我们告诉Gradle buildType=debug不要和flavor=distrib结合而buildType=release只和flavor=distrib结合，生成的Variants

![btFla](http://upload-images.jianshu.io/upload_images/186157-40e360cf1eed2fbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#3.Facilitate continuous integration（简化持续集成）

持续集成对于Applidium是一个大挑战，它是一种可以在开发的各个阶段保证你的代码质量的方法。下面是一些让集成CI更简单的技巧。

####Thanks to buildVariants

从上面的例子我们可以看到buildVariants(buildType+productFlavors)允许存在多个拥有各自资源和配置的开发环境。有了这些不同的开发环境，你可以很容易在假数据之间(stubs,suitable for tests)或者一个接近生产条件的环境(preprod)中进行切换。
####With a plug-in

	classpath 'com.novoda:gradle-android-command-plugin:1.4.0'

这个插件可以在Gradle tasks中执行adb命令，事实上，把所有的部署步骤集中在一个工具中是很重要的。这样，所有的部署链可以用一个单一的命令执行和最小化错误机率。当我们在Android Studio build+run构建运行一个应用程序时，Android Studio隐藏的事实是它使用了几个工具：首先执行Gradle assemble task然后通过adb安装和启动app。在CI Server或者其他你没有Android Studio的地方。你可以通过这个插件构建build和运行app

只需要在根目录的build.gradle添加一行即可

	dependencies {
	  classpath 'com.android.tools.build:gradle:1.3.0'
	  classpath 'com.jakewharton.sdkmanager:gradle-plugin:0.12.0'
	  classpath 'com.novoda:gradle-android-command-plugin:1.4.0'  //添加该行
	}

####Automatically increase the versionCode（版本号自增长）

为了容易发现问题，推荐让version code自增长，避免问题，你可以创建一个让version code以1递增的Gradle task。

下面是代码，添加到build.gradle里面

	void bumpVersionCodeInFile(File file) {
	    def text = file.text
	    def matcher = (text =~ /versionCode ([0-9]+)/)
	    if (matcher.size() != 1 || !matcher.hasGroup()) {
	        throw new GradleException("Could not find versionCode in app/build.gradle")
	    }
	    def String versionCodeStr = matcher[0][1]
	    def versionCode = Integer.valueOf(versionCodeStr)
	    def newVersionCode = versionCode + 1
	    def newContent = matcher.replaceFirst("versionCode " + newVersionCode)
	    file.write(newContent)
	}
	task(bumpVersionCode) << {
	    def appGradleFile = file('app/build.gradle')
	    if (appGradleFile.canRead()) {
	        bumpVersionCodeInFile(appGradleFile)
	    } else {
	        throw new GradleException("Could not read app/build.gradle");
	    }
	    def wearGradleFile = file('wear/build.gradle')
	    if (wearGradleFile.canRead()) {
	        bumpVersionCodeInFile(wearGradleFile)
	    }
	    // No exception here since projects are not required to have a wearable app
	}

#4.Tell us your interests（告诉我们你对哪些感兴趣）

这篇关于Gradle的文章到此结束了。接下来的主题还没确定，给我们分享你的想法或者你对Android还有哪些问题。我们将会尽力帮助你。


#5.其他资源      




[使用Gradle管理Debug/Release版本的Key](http://www.jianshu.com/p/3f25bef975e2) 使用了文中的动态占位符技术

[使用Gradle构建多个不同applicationId包](http://www.jianshu.com/p/037327da4893)使用了文中的productFlavor and buildConfigField同时打包多个开发环境包