>原文：[Best practices for SharedPreferences](http://blog.yakivmospan.com/best-practices-for-sharedpreferences/)

Android提供了很多种保存应用程序数据的方法。其中一种就是用SharedPreferences对象来保存我们私有的键值（key-value）数据。

所有的逻辑都是基于下面三个类：

- SharedPreferences
- SharedPreferences.Editor
- SharedPreferences.OnSharedPreferenceChangeListener

###SharedPreferences

SharedPreferences是其中最重要的。它负责获取（解析）存储的数据，提供获取Editor对象的接口和添加或移除OnSharedPreferenceChangeListener的接口。

- 创建SharedPreferences你需要Context对象（也可以是application Context）
- getSharedPreferences方法会解析Preference文件并为它创建一个Map对象
- 你可以用Context提供的几个模式创建它，强烈建议使用MODE_PRIVATE模式因为创建全局可读写的文件是比较危险的，可能会导致app的安全漏洞。

        / parse Preference file 解析Preference文件
        SharedPreferences preferences = context.getSharedPreferences("com.example.app", Context.MODE_PRIVATE);
        
        // get values from Map
        preferences.getBoolean("key", defaultValue)  
        preferences.get..("key", defaultValue)
        
        // you can get all Map but be careful you must not modify the collection returned by this
        // method, or alter any of its contents.
        //（Preference文件转换成map）你可以获取到一个map但是小心点最好不要修改map或它的内容
        Map<String, ?> all = preferences.getAll();
        
        // get Editor object
        SharedPreferences.Editor editor = preferences.edit();
        
        //add on Change Listener 添加监听器
        preferences.registerOnSharedPreferenceChangeListener(mListener);
        
        //remove on Change Listener 取消监听器
        preferences.unregisterOnSharedPreferenceChangeListener(mListener);
        
        // listener example 监听器例子
        SharedPreferences.OnSharedPreferenceChangeListener mOnSharedPreferenceChangeListener  
                = new SharedPreferences.OnSharedPreferenceChangeListener() {
            @Override
            public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
            }
        };

###Editor

SharedPreferences.Editor是一个用来修改SharedPreferences对象值的接口。所有在Editor的修改会进行批处理，同时只有你调用了commit()或者apply()的时候才会复制到原来的SharedPreferences。

- 用Editor的简单接口添加值
- 用同步的commit()或速度更快异步的apply()来保存值。实际上在不同的线程使用commit()时会更安全。这是为什么我喜欢用commit()
- 删除单个值用remove,删除所有值用clear()

        // get Editor object
        SharedPreferences.Editor editor = preferences.edit();
        
        // put values in editor
        editor.putBoolean("key", value);  
        editor.put..("key", value);
        
        // remove single value by key
        editor.remove("key");
        
        // remove all values
        editor.clear();
        
        // commit your putted values to the SharedPreferences object synchronously
        // returns true if success 同步提交保存 成功返回true
        boolean result = editor.commit();
        
        // do the same as commit() but asynchronously (faster but not safely)
        // returns nothing 异步保存 不返回结果
        editor.apply();

###性能和技巧

- SharedPreferences是一个单例对象所以你可以轻易的获取它的多个引用，它只有在第一次调用getSharedPreferences的时候打开文件，只为它创建一个引用。（ps:真啰嗦，其实就是只实例化一次，后面调用会越来越快，看下面的例子）

        // There are 1000 String values in preferences
        
        SharedPreferences first = context.getSharedPreferences("com.example.app", Context.MODE_PRIVATE);  
        // call time = 4 milliseconds第一次读取文件花了4毫秒
        
        SharedPreferences second = context.getSharedPreferences("com.example.app", Context.MODE_PRIVATE);  
        // call time = 0 milliseconds第二次0
        
        SharedPreferences third = context.getSharedPreferences("com.example.app", Context.MODE_PRIVATE);  
        // call time = 0 milliseconds第三次0

- 因为是单例对象你可以随意更改它多个实例的内容不用担心他们的数据会不同

        first.edit().putInt("key",15).commit();
        
        int firstValue = first.getInt("key",0)); // firstValue is 15  
        int secondValue = second.getInt("key",0)); // secondValue is also 15

- 当你第一次调用get方法时，它会通过key解析获取到value然后会把它加到map中，第二次调用get会直接从map拿出，不用解析。

        first.getString("key", null)  
        // call time = 147 milliseconds 第一次拿需要解析比较慢，然后会放到map中
        
        first.getString("key", null)  
        // call time = 0 milliseconds 第二次直接从map中拿 ，不用解析 很快
        
        second.getString("key", null)  
        // call time = 0 milliseconds 和第二次一样
        
        third.getString("key", null)  
        // call time = 0 milliseconds

- 记住越大的Preference对象它的get,commit,apply,remove和clear等操作时间越长。所以推荐把你的数据分割成不同的小对象。
- 你的Preference不会在你的app更新后移除，所以有时候你需要创建一个迁移方案。例如你的app需要在启动的时候解析本地的JSON,只有在第一次启动执行然后保存boolean的标识wasLocalDataLoaded,一段时间后你更新了JSON发布了一个新版本，用户会更新app但是他们不会加载新的JSON因为他们在第一个版本已经做了。

        public class MigrationManager {  
            private final static String KEY_PREFERENCES_VERSION = "key_preferences_version";
            private final static int PREFERENCES_VERSION = 2;
        
            public static void migrate(Context context) {
                SharedPreferences preferences = context.getSharedPreferences("pref", Context.MODE_PRIVATE);
                checkPreferences(preferences);
            }
        
            private static void checkPreferences(SharedPreferences thePreferences) {
                final double oldVersion = thePreferences.getInt(KEY_PREFERENCES_VERSION, 1);
        
                if (oldVersion < PREFERENCES_VERSION) {
                    final SharedPreferences.Editor edit = thePreferences.edit();
                    edit.clear();
                    edit.putInt(KEY_PREFERENCES_VERSION, currentVersion);
                    edit.commit();
                }
            }
        }

- SharedPreferences保存在app的data文件夹下xml文件里面

        // yours preferences 我们自己创建的
        /data/data/YOUR_PACKAGE_NAME/shared_prefs/YOUR_PREFS_NAME.xml
        
        // default preferences 默认
        /data/data/YOUR_PACKAGE_NAME/shared_prefs/YOUR_PACKAGE_NAME_preferences.xml

###示例代码

        public class PreferencesManager {
        
            private static final String PREF_NAME = "com.example.app.PREF_NAME";
            private static final String KEY_VALUE = "com.example.app.KEY_VALUE";
        
            private static PreferencesManager sInstance;
            private final SharedPreferences mPref;
        
            private PreferencesManager(Context context) {
                mPref = context.getSharedPreferences(PREF_NAME, Context.MODE_PRIVATE);
            }
        
            public static synchronized void initializeInstance(Context context) {
                if (sInstance == null) {
                    sInstance = new PreferencesManager(context);
                }
            }
        
            public static synchronized PreferencesManager getInstance() {
                if (sInstance == null) {
                    throw new IllegalStateException(PreferencesManager.class.getSimpleName() +
                            " is not initialized, call initializeInstance(..) method first.");
                }
                return sInstance;
            }
        
            public void setValue(long value) {
                mPref.edit()
                        .putLong(KEY_VALUE, value)
                        .commit();
            }
        
            public long getValue() {
                return mPref.getLong(KEY_VALUE, 0);
            }
        
            public void remove(String key) {
                mPref.edit()
                        .remove(key)
                        .commit();
            }
        
            public boolean clear() {
                return mPref.edit()
                        .clear()
                        .commit();
            }
        }