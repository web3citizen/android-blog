这个架构已经有不少文章介绍了，今天打算自己实践下。

MVP概念不多说了 相关介绍已经很多了 

Dagger2：依赖注入框架，用来解决依赖 除了基本依赖 ，mvp的V-->P-->M的之间依赖也轻松解决 方便不少 

Retrofit：用来解决M的RestApi数据获取，  天然支持Rxjava  不过这里我没用到Rxjava 其自带的Callback已经足够用了

估计这个架构的难点在于Dagger2 理解它的工作方式需要方式需要点时间，我收集了一些资料，可以查看我博客的相关文章。


ok,开始写demo,简单看下项目结构

![项目结构](http://img.blog.csdn.net/20150613224358959)

 - data:数据来源 也就是MVP的M
 - model:实体bean，本来也应该放在data中，为了方便查找就抽出了，毕竟很多地方用到
 - ui:视图，V和P都在里面，View层是最难组织的，有人喜欢以功能划分 eg:main,login 有人喜欢以组件划分
 

![完整项目结构](http://img.blog.csdn.net/20150613225224892)



来简单看一下dagger2提供的依赖结构

![dagger依赖结构](http://img.blog.csdn.net/20150613230845776)

AppComponent通常提供全局的对象以便于其他的组件依赖使用，比如context,rest api接口等，这些都是全局的单例对象

MainActivityComponent特意针对MainActivity，所以它只提供其对应的MainActivityPresenter，因为其依赖AppComponent，所以它可以使用AppComponent提供的对象

**AppComponent**
可以看到其向外公布了Application,ApiService,User三个对象，供其他component都可以使用

	@Singleton
	@Component(modules = {AppModule.class, ApiServiceModule.class, AppServiceModule.class})
	public interface AppComponent {
	
	
	    Application getApplication();
	
	    ApiService getService();
	
	    User getUser();
	}


**MainActivityComponent**
它依赖了AppComponent,本身只提供了MainActivityPrresenter,并且只针对MainActivity

	@ActivityScope
	@Component(modules = MainActivityModule.class,dependencies = AppComponent.class)
	public interface MainActivityComponent {
	
	    MainActivity inject(MainActivity mainActivity);
	
	    MainActivityPresenter presenter();
	
	
	}

最后看一下Activity怎么使用


	public class MainActivity extends BaseActivity {
	
	    @InjectView(R.id.tv)
	    TextView textView;
	
	    @Inject
	    MainActivityPresenter presenter;//注入其所需要的presenter
	
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	        ButterKnife.inject(this);
	        presenter.showUserName();
	
	    }
	
	    @Override
	    protected void setupActivityComponent(AppComponent appComponent) {
	        DaggerMainActivityComponent.builder()
	                .appComponent(appComponent)
	                .mainActivityModule(new MainActivityModule(this))
	                .build()
	                .inject(this);
	
	    }
	
	//只做界面逻辑
	    public void setTextView(String username){
	        textView.setText(username);
	    }
	
	
	
	
	}


代码就不全贴了 后面会放到github上面去
显而易见，整个项目和结构会非常舒服，各个模块各司其事，有了dagger在扩展和测试会非常方便，分别提供DebugComponent和ReleaseComponent随意切换