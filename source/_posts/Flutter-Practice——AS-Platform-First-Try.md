--- 
title: Flutter实践——AndroidStudio环境初体验
date: 2018-12-29 22:38:47
tags:
--- 
&emsp;&emsp;这是一篇菜鸟萌新初次上手Flutter的实践过程记录，老鸟请自行略过哈~最近大家都在说Flutter，还不是因为Google爸爸刚刚发布了Flutter 1.0 版本，简单了解了下，类似于微信小程序等等的一种跨平台解决方案，操作流畅度据说不管是iOS，还是Android，都可以“如丝般顺滑”，这么腻害？赶紧入坑看一看~

## Flutter简介
&emsp;&emsp;简介啥的自己查一查看看就好，不过这一篇还是比较好的，传送门：[Flutter - 不一样的跨平台解决方案](https://www.jianshu.com/p/eff00093ed49) 。老实说这篇实践就是根据它来的，哈哈哈哈，感谢大佬！

## Flutter安装配置
&emsp;&emsp;本文介绍的是在Windows10系统上AndroidStudio中安装配置Flutter的方法和步骤，其他系统环境欢迎查看参考文献1.
&emsp;&emsp;[Flutter中文网](https://flutterchina.club/setup-windows/)是个不错的网站，居然有中文文档不错不错，自学能力不错的可以自己捣鼓。首先，要添加环境变量到自己的用户环境变量中。国内访问Flutter有时可能会受到限制（会科学上网的可自行忽略），Flutter官方就为中国开发者搭建了临时镜像下载window版本的Flutter资源文件，大家可以将如下环境变量加入到用户环境变量中：
```
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```
&emsp;&emsp;这个是Linux系统的命令，在windows系统中也很简单，就是配置环境变量：
![image1.png](https://upload-images.jianshu.io/upload_images/3623770-09625a54a80889f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image2.png](https://upload-images.jianshu.io/upload_images/3623770-6fe7146feb75415a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**注意：**这镜像还只是临时的，万一哪天和组织联系不上了，还有组织留下的线索——[Using Flutter in China](https://flutter.io/community/china)。
上面环境变量设置完之后就可以开始下载Flutter安装包了。
[Using Flutter in China](https://flutter.io/community/china)这个网址不仅有官方下载原地址，还有镜像下载地址：
Original URL: 
 [https://storage.googleapis.com/flutter_infra/releases/stable/windows/flutter_windows_v1.0.0-stable.zip](https://storage.googleapis.com/flutter_infra/releases/stable/windows/flutter_windows_v1.0.0-stable.zip)  
Mirrored URL:  
[https://storage.flutter-io.cn/flutter_infra/releases/stable/windows/flutter_windows_v1.0.0-stable.zip](https://storage.flutter-io.cn/flutter_infra/releases/stable/windows/flutter_windows_v1.0.0-stable.zip)
我是用镜像地址下载的，速度很快，亲试有效。
不仅如此，这个网址还提供了上海交通大学的镜像地址，如果之前设置的地址不能访问，可以设置这个地址，设置方法和之前环境配置一样：
`FLUTTER_STORAGE_BASE_URL`: [https://mirrors.sjtug.sjtu.edu.cn/](https://mirrors.sjtug.sjtu.edu.cn/)
`PUB_HOSTED_URL`: [https://dart-pub.mirrors.sjtug.sjtu.edu.cn/](https://dart-pub.mirrors.sjtug.sjtu.edu.cn/)
&emsp;&emsp;将Flutter下载完成之后，将其解压放在权限较低的文件目录中，不建议放在X:/Program Files目录下，建议放在别的目录中：
![image3.png](https://upload-images.jianshu.io/upload_images/3623770-5a4391f55c4adf94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
&emsp;&emsp;然后进入目录中，双击打开 **flutter-console.bat** ，进入Flutter命令行窗口：
![clipboard.png](https://upload-images.jianshu.io/upload_images/3623770-b7e17d46ba7d0412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
图中可以看出怎样在cmd中直接运用flutter的命令，无非就是将flutter的路径添加到系统环境变量中。
&emsp;&emsp;输入命令检查Flutter安装情况：
```
flutter doctor
```
出现XX或者！的地方都是有问题的，比如说我这里有4个问题：
- Some Android licenses not accepted 
- Flutter plugin not installed
- Dart plugin not installed
- No devices available

![clipboard1.png](https://upload-images.jianshu.io/upload_images/3623770-3587b0de775d042c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
第一个问题，只需要如图中所示执行下面命令，然后一路输入“y”同意即可：
```
flutter doctor --android-licenses
```
在这里我遇到了一个问题，提醒说
>A newer version of the Android SDK is required. To update, run:
/Users/iOSCMB/AndroidStudio/SDK/tools/bin/sdkmanager --update

但是当我在命令行中执行这个命令的时候，又报错。报错信息是：
>Warning:An error occurred during installation:Failed to move away or delete existing target file: D:\User\XXX\Android\SDK\tools
Move it away manually and try again..

千万不要按照它所说的把这个目录给删除，这是没有给Android SDK授权的原因，我们可以在命令后加 `-v` 来查看更详细的报错信息：
```
sdkmanager --update -v
```
![22.jpg](https://upload-images.jianshu.io/upload_images/3623770-6826de3654b6fff7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
根据它所说的，先给它授权，执行下面的命令：
```
sdkmanager --licenses
```
然后一路授权就可以了。
![menu.saveimg.savepath20181228224925.jpg](https://upload-images.jianshu.io/upload_images/3623770-2f996de29c6d7135.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来的两个问题就要在AndroidStudio中解决了。

## AndroidStudio安装Flutter和Dart插件
&emsp;&emsp;在安装这两个插件之前，确保自己的AndroidStudio版本在3.1以上，我自己用的是3.2.1的版本，打开File—Settings—Plugins，在搜索框中搜索Flutter，点击install进行安装，Dart插件会一并进行下载安装。如果无法下载，可以将File->Settings->Apparence & Behavior->System Settings->Updates->use secure connnection 勾去掉，我就出现了这个问题，去掉之后就可以下载插件了。
&emsp;&emsp;都安装之后重启IDE，就可以发现New下面多了创建Flutter Project入口：
![image4.png](https://upload-images.jianshu.io/upload_images/3623770-0bc963a02dac1bc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这就说明安装成功，然后连接手机，打开USB调试，再输入flutter doctor进行检测，就会发现没有问题了。

## Flutter Hello World
&emsp;&emsp;建议编写Flutter程序在AndroidStudio工具上进行，与Android开发类似，支持断点调试等。在用AS正式开始之前要配置一下Flutter的SDK，如图所示：
![7.jpg](https://upload-images.jianshu.io/upload_images/3623770-cae3c607cc24f096.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上述完成之后，就可以正式开始了。新建一个Flutter Project时，有四种方式：Flutter Application、Flutter Plugin、Flutter Package、Flutter Module
![image5.png](https://upload-images.jianshu.io/upload_images/3623770-d3788f3f140a4087.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里直接选择默认第一个Flutter Application就行，进去之后大概就是这个样子：
![image.png](https://upload-images.jianshu.io/upload_images/3623770-df919533c2a1b67c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
&emsp;&emsp;这里有一定的概率会卡死在创建项目的界面，可以唤起资源管理器强制停止，然后再重新从创建好的flutter项目中打开，根据指引 run 一下dart的资源就可以了。
**注意**：连接真机后部署，可能会部署很长一段时间，卡在 initializing gradle 很久，这是因为它默认是要下载最新的Gradle版本的，而不是科学上网的你...就需要等很久很久，如果你打开你的C:\Users\你的用户名\.gradle\wrapper\dists 就会发现多了存放最新Gradle版本的一个文件夹，比如我就多了“gradle-4.10.2-all”这个文件夹，最简单的方法就是将这个新建Flutter项目所用的Gradle版本设置成已有的Gradle版本，这里我改成了已有的gradle-4.6的版本。当然也可以用文献4的方法进行修改。
![image.png](https://upload-images.jianshu.io/upload_images/3623770-27bff4cbe61d425f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
&emsp;&emsp;OK，到此就可以成功部署了，我感觉部署的时间还是有点长的，不知是不是第一次编译的原因。Enjoy Yourself ！
![8.jpg](https://upload-images.jianshu.io/upload_images/3623770-1a45e862b0e2489f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 参考文献
1、[Flutter - 不一样的跨平台解决方案](https://www.jianshu.com/p/eff00093ed49)
2、[Using Flutter in China](https://flutter.io/community/china)
3、[android studio 无法下载插件](https://blog.csdn.net/qq_37405874/article/details/80341606)
4、[Flutter在Android Studio上的初启动](https://blog.csdn.net/jxq1994/article/details/81623893)