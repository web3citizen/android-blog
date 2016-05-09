>原文链接：[How to make your own File Templates in Android Studio]()

![image]()

我最近发现一个可以让我生活更简单的东东：自定义模板文件。什么是模板文件？模板文件是已经包含了一些代码的源文件。

在下面的例子中，我们将会创建经常使用到的RecycleView adapter实现模板文件。

#How to create your own file template in Android Studio:

1.右键代码文件，选择“New” 然后再点击“Edit File Templates”

![step 1]()

2.点击添加按钮 新建一个新模板

![step 2]()

3.你需要给模板文件命名，因为我打算创建RecycleView adapter的模板，所以命名成RecyclerViewAdapter.

4.然后你要粘贴或填写你的模板代码，这里你可以使用一些变量。例如：

- ${NAME} 文件名字
- ${PACKAGE_NAME} 包名
- ${DATE} 系统当前时间

你也可以自定义一些变量让用户输入，在这个例子中，我想要用户提供ViewHolder class所以用${VIEWHOLDER_CLASS} 列表item用${ITEM_CLASS}

现在下面的代码用来创建Recycler Adapter实现模板：

当你使用这个模板的时候，会被提示输入${VIEWHOLDER_CLASS},${ITEM_CLASS}的值，这些值会替代我们定义的变量占位符。


5.想使用你定义好的模板，点击右键 点击“New” 然后就会看到你模板名字出现在列表中。

![]()

点击模板名字，然后填写你的变量值：

![]()

然后你就会看到一个漂亮的生成好的class

现在我再也不用查找RecyclerView adapters了！

你有其他好用的模板吗？分享给我吧。
