>原文：[Android Project Structure — alternative way](https://medium.com/@dmytrodanylyk/android-project-structure-alternative-way-29ce766682f0#.onofequfx)

>译者注：我很喜欢工工整整的摆放哪些图片什么的，看的舒服，因为真的是太多了 要找或者替换的时候很烦，所以项目中我用了Android File Grouping这个插件 很好用 但是它只是解决了layout文件，没有解决其他资源文件分组，这篇文章提供了非常好的方法，不说了 去把我的项目重构一遍。

我们都知道android项目结构长成下面这样：所有的图片放在drawble相应文件夹，所有的布局文件放在layout文件夹。但是随的项目开发文件越来越多，慢慢的会发现导航和查找文件变得非常困难。

![Typical android project structure](https://cdn-images-1.medium.com/max/800/1*VbZ-FXtEPpcsCZMdDXof3A.png)

###一个界面一个资源文件夹

在这种情况下你的界面包含大量的layout,drawable,demension，那么有理由给你的每个界面建立一个单独的资源文件夹

![Resource folder — per screen android project structure](https://cdn-images-1.medium.com/max/800/1*qBwnEgU3Mmjwfy9yIM4NiQ.png)

从上面的图片我们可以看到2个根文件夹包含在main目录中：

-  *res-main* 包含那些在多个界面中用到的公共资源
-  *res-screen* 包含各个界面的资源文件夹 例如：about,chat,event,details,event list,home,login等界面资源文件夹

让我们看一下chat界面的资源文件夹

![Resource folder](https://cdn-images-1.medium.com/max/800/1*ZF5Qi_OuNPHglNg21RYGaA.png)

chat本身包含几个layout xml文件，所以我们创建了chat->layout文件夹并把它们都移到里面。如果还有很多只在chat界面用到的png图片，我们也把它们移到chat->drawable-hdpi,chat->drawable-xhdpi,chat->drawable-xxhdpi和chat->drawable-xxxhdpi对应文件夹里面。

当以后chat界面需要实现水平布局或者支持平板的时候，我们会创建layout-land和layout-sw720文件夹放在chat目录下。

### 怎么让gradle识别我们的资源文件夹

打开app.gradle文件在android块中声明sourceSets.更多资源合并信息请查看[here](http://tools.android.com/tech-docs/new-build-system/resource-merging)

    sourceSets {
        main {
            res.srcDirs = [
                    'src/main/res-main',
                    'src/main/res-screen/about',
                    'src/main/res-screen/chat',
                    'src/main/res-screen/event-detail',
                    'src/main/res-screen/event-list',
                    'src/main/res-screen/home',
                    'src/main/res-screen/login',
            ]
        }
    }

> **注意**：上面所有的操作确保你在Project模式下进行

###总结

如果你的项目很大，并且想要组织你的文件夹以便快速查看相关界面的layout,drawables,values等等，最好尝试使用每屏一个资源文件夹的android项目结构。