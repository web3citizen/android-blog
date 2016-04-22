>原文：[Add a copyright notice to your code](https://antoniocappiello.com/2015/12/08/add-a-copyright-notice-to-your-code/)

>译文的GitHub地址：

>译者注：一个规范的项目无论是开源的还是公司内部，版权声明和协议声明都是不可少的，在Android Studio中非常简单，没加上的快去给你的项目加上吧。

![image](https://antoniocappiellocomblog.files.wordpress.com/2015/12/how-i-did-it-last-time-omer.jpg?w=322&h=239)

写这个“上次我是怎么做的”系列，是为了下次再遇到不需要去google了。

##怎么给我的android项目添加自定义的版权声明呢？

其实非常简单的，你只需要记住哪个menu和修改哪个设置就可以了。

首先，你需要确定版权是要给android studio中所有项目还是只是给当前打开的项目。

要是给所有的项目，选择File->Default Settings

![all_project](https://antoniocappiellocomblog.files.wordpress.com/2015/12/preferences-global.png?w=676)

要是只是给当前的项目，只需点击如下图的Preference图标

![current_project](https://antoniocappiellocomblog.files.wordpress.com/2015/12/preferences-current-project.png?w=676)

接下来的步骤都是一样，除了上面开头那段

找到Editor->Copyright,点击Copyright Profiles
![step 2](https://antoniocappiellocomblog.files.wordpress.com/2015/12/preferences-current-project-2.png?w=676)

点击打开面板左侧的“+”图标，新建一个你自己的版权声明

![step 3](https://antoniocappiellocomblog.files.wordpress.com/2015/12/new.png?w=676)

输入你的版权文本，还有你可以你的文本中使用一些变量 例如：

- $today:当前的日期和时间
- $today.year:当前年
- $username:android studio当前用户名
- 还有[其他](https://www.jetbrains.com/help/idea/2016.1/copyright-profiles.html?origin=old_help)

![step 4](https://antoniocappiellocomblog.files.wordpress.com/2015/12/formatting.png?w=676)

填写完后，点击验证*Validate*按钮确保它可以符合velocity模板，如果合法，会弹出下面的提示，然后点击Apply

![step 5](https://antoniocappiellocomblog.files.wordpress.com/2015/12/validation.png?w=676)

完成后再回来点击*Copyright*，现在你可以在下拉框里面选择你刚新建的版权，然后点击*Apply/Ok*

![step 6](https://antoniocappiellocomblog.files.wordpress.com/2015/12/apply.png?w=676)

现在你的版权信息已经可以用了，如果你新建一个文件，它会自动添加到文件的顶部。

![result](https://antoniocappiellocomblog.files.wordpress.com/2015/12/result.png?w=676)

但是有种情况怎么给现有的文件手动添加呢？，在文件顶部右键点击弹出菜单->选择*Generata...*如下图所示:

![generata](https://antoniocappiellocomblog.files.wordpress.com/2015/12/generate.png?w=676)

将会出现一个小窗口

![mini](https://antoniocappiellocomblog.files.wordpress.com/2015/12/generate-2.png?w=676)

点击*Copyright*然后版权信息会自动添加到文件的最上面！


显然你不会想要给所有现有的文件一个一个手动添加，所以我建议在项目任何文件夹右键点击出现下图，然后选择*Update  Copyright...*

![update all](https://antoniocappiellocomblog.files.wordpress.com/2015/12/update-all.png?w=676)

这时候你可以选择把你的版权声明添加到整个项目中。

![all project](https://antoniocappiellocomblog.files.wordpress.com/2015/12/update-all-2.png?w=676)

好啦！

非常简单，写下来之后 后面再遇到就知道怎么做了。