>原文：[Component Dependency vs Submodules in Dagger 2](http://jellybeanssir.blogspot.my/2015/05/component-dependency-vs-submodules-in.html)

>译文的GitHub地址：[dagger2：组件依赖和子组件的区别](https://github.com/thinkSky1206/android-blog/blob/master/dagger2%EF%BC%9A%E7%BB%84%E4%BB%B6%E4%BE%9D%E8%B5%96%E5%92%8C%E5%AD%90%E7%BB%84%E4%BB%B6%E7%9A%84%E5%8C%BA%E5%88%AB.md)

>译者注：发现很多人都忽略了这个重要的内容，没有完全翻译原文，只简单翻译了重点。有兴趣的可以查看原文

#Component Dependency(组件依赖)

假如，你有一个ApplicationComponent持有一个Module里面包含LocationService,Resources,LayoutInflater等等，它们都需要Context参数。

你可能也想有一个DataComponent用来管理持久化WebService拿到的数据。

可DataComponent唯一缺的就是ApplicationComponent里面的Context.这就是Component Dependecy发挥作用的地方。你只需要给DataComponent声明依赖Application Component然后你就可以拿到Context了。

记住，你必须要在ApplicationComponent显示声明Context 因为这是消费父组件对象的唯一方法。它所有modules里面的对象不能用于child module

例子：

	@Singleton
	@Component(modules = ApplicationModule.class)
	public interface ApplicationComponent {
	
		//显示声明 对子组件可见
	    Application application();
	}

	@PerActivity
	//声明依赖
	@Component(dependencies = ApplicationComponent.class, modules = ActivityModule.class)
	public interface ActivityComponent {
	
	    void inject(MainActivity mainActivity);
	
	}

#Subcomponents(子组件)

和组件依赖最大的不同是当你接入父组件的时候，你可以访问它所有模块的所有对象而无需显示声明依赖父组件。

让我们假设你的LoginFragment需要一个组件：LoginComponent,它包含了一个用于处理的Presenter。Presenter想要调用WebService的方法获取数据，所以它需要依赖DataComponent里面的WebService.

这次你不用在LoginComponent中指定任何东西，但是在ApplicationComponent,你需要声明ApplicationComponent可以被LoginComponent继承如下：

	LoginComponent plus(LoginModule module);

然后你需要手动hook into ApplicationComponent来获取它所有的对象：

	getApplicationComponent().plus(new LoginModule(this));

不要忘了把你的子组件@Component替换成@Subcomponent。

例子：

	@Singleton
	@Component(modules = AppModule.class)
	public interface AppComponent {
	
		//activy组件继承父组件
	    ActivityComponent plus(ActivityModule module);
	
	}

	@ActivityScope
	//声明Subcomponent
	@Subcomponent(modules = ActivityModule.class)
	public interface ActivityComponent {
	
	    void inject(MainActivity activity);
	
	    void inject(SearchActivity activity);

	    FragmentComponent plus(FragmentModule module);
	
	}
