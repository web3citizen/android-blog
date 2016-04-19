>原文：[Gradle dependencies](https://applidium.com/en/news/gradle_dependencies/)

>译者：个别地方翻译可能不是很好，反复读了几遍，对自己帮助还是挺大的。

前段时间我们谈了[Gradle](http://www.jianshu.com/p/cc98a6b4f52e),今天我们将会讨论Gradle的依赖管理，对于Android来说是一个伟大的功能。

![gradle](https://applidium.com/en/news/gradle_dependencies/android_studio.png?14582343)

使用Gradle你可以用类似Maven的方式轻松管理一些依赖像Play Services,support libraries,或者其他库。既然这样，那么你可以反问自己一些关于管理依赖的问题：

- 当一个项目中的2个依赖同时依赖某个依赖，而它们的各自依赖的版本号有冲突的时候，怎么进行管理？(a，b同时依赖c ,a依赖c1.1,b依赖c2.1)
- 什么是传递依赖解析？
- 可选依赖的作用？

本文会用一些我们熟悉的例子清晰得来解决这些问题。

###演示项目

本文讨论的例子都放在了[Github](https://github.com/applidium/gradle-dependencies-demo)上面，你可以clone下来自己尝试一下。

###怎么添加依赖

####例子
[Calligraphy]()是一个知名的管理字体的android库，为了用它，你需要在项目build.gradle添加它作为项目的一个依赖：

	compile 'uk.co.chrisjenx:calligraphy:2.1.0'

这是一个很典型用来添加依赖的例子，但是它自身有其他依赖呢？为了列出你项目中所有的依赖（包含依赖的子依赖）你可以用下面这个命令：

	./gradlew dependencies app:dependencies

这个命令会列出app module每一个“configuration”（https://docs.gradle.org/current/dsl/org.gradle.api.artifacts.Configuration.html）的依赖，为了减少输出内容我们只对compile configuration感兴趣。

	./gradlew dependencies app:dependencies --configuration compile

我们获取到下面的结果（译者：自己可以查看下自己控制台输出更清晰）：

	compile - Classpath for compiling the main sources.
	--- uk.co.chrisjenx:calligraphy:2.1.0
	\--- com.android.support:appcompat-v7:22.1.1
	\ --- com.android.support:support-v4:22.1.1
	\  --- com.android.support:support-annotations:22.1.1

我们可以看到Calligraphy依赖appcompat-v7，而v7本身又依赖appcompat-v4，v4反过来又依赖support-annotations，这些依赖不用我们添加默认就解决好了，它们被传递解决了。

###可传递性依赖
####例子
要是我们的app module已经使用了appcompat-v7，但是，是更高版本23.1.1，那么最终app使用的是哪个版本的appcompat-v7，是Calligraphy依赖的22.1.1吗？让我们运行命令再来分析下：

	compile - Classpath for compiling the main sources.
	+--- uk.co.chrisjenx:calligraphy:2.1.0
	|    --- com.android.support:appcompat-v7:22.1.1 -> 23.1.1
	|         --- com.android.support:support-v4:23.1.1
	|              --- com.android.support:support-annotations:23.1.1
	--- com.android.support:appcompat-v7:23.1.1 (*)

calligraphy依赖的appcompat-v7：22.1.1已经变成了appcompat-v7：23.1.1，默认，依赖解析是可传递的，也就是以递归的方式解析所有依赖(包括依赖的依赖)，如果有冲突则取高的版本号。这就是为什么support-v4和support-annotations会自动引入到我的app module（我们没有必要在build.gradle进行添加它们了）

###依赖解析策略
####例子
我们可以添加下面的代码块到我们的build.gradle中用来通知我们是否项目的同一个依赖有2个不同版本：

	configurations.all {
	  resolutionStrategy {
	    failOnVersionConflict()
	  }
	}
再build时上面的代码会触发错误，强制我们手动解决版本冲突。默认情况下，Gradle用它[自己的依赖解析策略](https://docs.gradle.org/current/userguide/dependency_management.html#sub:version_conflicts),当发生版本冲突时，它会使用高版本号，你也可以定义自己的解析策略，类似上面这个([文档](https://docs.gradle.org/current/dsl/org.gradle.api.artifacts.ResolutionStrategy.html))

###过滤依赖
####例子
此外，我们可以直接配置Gradle怎样建立依赖，例如，如果查看[simple-xml](http://simple.sourceforge.net/home.php)库，依赖分析结果如下：

	compile - Classpath for compiling the main sources.
	--- org.simpleframework:simple-xml:2.7.1
	+--- stax:stax-api:1.0.1
	+--- stax:stax:1.2.0
	|    \--- stax:stax-api:1.0.1
	\--- xpp3:xpp3:1.1.3.3

配置是无法通过编译，因为下面的原因：

- xpp3本身就直接被包含在Android framework中，我们不能包含同包名，同类名的另外一个版本的xpp3
- stax用的是android framework受保护的包名(java.xml.*)，然而不用担忧simple-xml可以工作在Android上因为它的依赖stax是可选的
（stax的可用性是在[运行时被检查](http://grepcode.com/file/repo1.maven.org/maven2/org.simpleframework/simple-xml/2.7.1/org/simpleframework/xml/stream/ProviderFactory.java/)）

但是我们需要修改build.gradle以便编译期间忽略stax(顺便说一下，编译系统足够聪明会自动忽略xpp3,只是会显示一个警告)，我们可以用下面的语法排除stax

	compile('org.simpleframework:simple-xml:2.7.1'){
	  exclude module: 'stax'
	  exclude module: 'stax-api'
	  exclude module: 'xpp3' // optional
	}

相应的DSL文档请查看[这里](https://docs.gradle.org/current/dsl/org.gradle.api.artifacts.dsl.DependencyHandler.html)
###给library开发者
有了前面的例子我们可以看到没有stax，simple-xml也可以工作，如果我们看一下它的pom.xml，我们会发现stat不是一个可选依赖。但是它却可以定义为一个可选依赖。让我们细想一下：一个library L依赖一个可选的library O,L传递依赖解析的时候不会包含O依赖。

在simple-xml的例子中，如果stax和xpp3是可选依赖，我们就可以直接像下面声明simple-xml依赖：

	compile 'org.simpleframework:simple-xml:2.7.1'

实际上，Retrofit1就是这种情况，OKHttp是一个可选的依赖。那么在运行时，如果OkHttp可用，Retrofit会使用OkHttp作为默认的http client,否则将使用Retrofit用到的平台提供的默认http client,我们还注意到Retrofit 1没有使用Gradle而是Maven。我们可以解释这个选择是因为声明可选依赖还没有被Gradle原生支持。但是目前有一些解决方案可以让你实现：

- 如[这里](https://issues.gradle.org/browse/GRADLE-1749)解释，可以手动使用可选依赖
- 这个插件([gradle-extra-configurations-plugin](https://github.com/nebula-plugins/gradle-extra-configurations-plugin))可以给Gradle build system带来可选依赖
###总结
通过Gradle进行依赖管理是非常有帮助的。清晰的理解怎么配置这些依赖可以让你以一种有意义的方式管理依赖关系在android项目周期的演变。
不仅如此，一个非常精确的库依赖配置对开发人员帮助很大，可以很容易地包括在其他项目，如Retrofit.

特别感谢Adrien Couque给予我的帮助。

###相关资源
[Gradle的高级技巧](http://www.jianshu.com/p/cc98a6b4f52e#)