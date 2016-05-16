>原文链接：[How to make your own File Templates in Android Studio](http://riggaroo.co.za/custom-file-templates-android-studio/)

>译文的GitHub地址：[Android Studio自定义文件模板](https://github.com/thinkSky1206/android-blog/blob/master/Android%20Studio%E8%87%AA%E5%AE%9A%E4%B9%89%E6%96%87%E4%BB%B6%E6%A8%A1%E6%9D%BF.md)

>译者注：还是挺实用的

![image](http://i1.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/androidstudiofiletemplates.png)

我最近发现一个可以让我生活更简单的东东：自定义文件模板。什么是文件模板？文件模板是一开始就已经包含了一些代码的源文件。

在下面的例子中，我们将为用到时经常要查找的RecycleView adapter实现创建模板文件。

#How to create your own file template in Android Studio:

1.右键代码源文件夹，选择“New” 然后再点击“Edit File Templates”

![step 1](http://i0.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/CustomFileTemplates1.png)

2.点击添加按钮 新建一个新模板

![step 2](http://i0.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/CreateNewTemplate.png)

3.你需要给模板文件命名，因为我打算创建RecycleView adapter的模板，所以命名成RecyclerViewAdapter.

4.然后你要粘贴或填写你的模板代码，这里你可以使用一些变量。下面是一些预定义变量：

- ${NAME} 文件名字
- ${PACKAGE_NAME} 包名
- ${DATE} 系统当前时间

你也可以自定义一些变量让用户输入，在这个例子中，我想要用户提供ViewHolder class所以用${VIEWHOLDER_CLASS} 列表item class用${ITEM_CLASS}

现在下面的代码用来创建Recycler Adapter实现模板：

	#if (${PACKAGE_NAME} && ${PACKAGE_NAME} != "")package ${PACKAGE_NAME};#end
	
	import android.content.Context;
	import android.support.v7.widget.RecyclerView;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;
	import java.util.List;
	
	#parse("File Header.java")
	public class ${NAME} extends RecyclerView.Adapter<${VIEWHOLDER_CLASS}> {
	    private final Context context;
	    private List<${ITEM_CLASS}> items;
	   
	    public ${NAME}(List<${ITEM_CLASS}> items, Context context) {
	        this.items = items;
	        this.context = context;
	    }
	
	    @Override
	    public ${VIEWHOLDER_CLASS} onCreateViewHolder(ViewGroup parent,
	                                             int viewType) {
	        View v = LayoutInflater.from(parent.getContext())
	                .inflate(R.layout.${LAYOUT_RES_ID}, parent, false);
	        return new ${VIEWHOLDER_CLASS}(v);
	    }
	
	    @Override
	    public void onBindViewHolder(${VIEWHOLDER_CLASS} holder, int position) {
	        ${ITEM_CLASS} item = items.get(position);
	        //TODO Fill in your logic for binding the view.
	    }
	
	    @Override
	    public int getItemCount() {
	        if (items == null){
	            return 0;
	        }
	        return items.size();
	    }
	}

当你使用这个模板的时候，会被提示输入${VIEWHOLDER_CLASS},${ITEM_CLASS}的值，这些值会替代我们定义的变量占位符,非常方便。


5.想使用你定义好的模板，点击右键 点击“New” 然后就会看到你模板名字出现在列表中。

![image](http://i2.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/Selecting-custom-template.png)

点击模板名字，然后填写你的变量值：

![submit value](http://i2.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/FillInCustomTemplateVariables.png)

然后你就会看到一个漂亮的生成好的class

![generated class](http://i1.wp.com/riggaroo.co.za/wp-content/uploads/2016/05/GeneratedClassFromTemplate.png)

现在我再也不用去查找RecyclerView adapters了！耶！

你有其他在用的好用模板吗？分享给我吧。
