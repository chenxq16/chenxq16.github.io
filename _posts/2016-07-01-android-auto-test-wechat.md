---
layout: post
title:  android自动化测试入门实践
subtitle:   "利用Appium实现批量与微信附近的人say Hi"
author: "MakerChen"
# header-img: ""
date:   2017-07-01 17:50:00
tags:
    - android
---

## android自动化测试入门实践
### --- 利用Appium实现批量与微信附近的人say Hi

### 环境搭建

#### Node.js环境

相对简单，下载对应操作系统的版本，傻瓜式安装。
<a href="https://nodejs.org/en/download/">https://nodejs.org/en/download/</a>

#### Appium环境

相对简单，下载对应操作系统的版本，傻瓜式安装。
<a href="https://bitbucket.org/appium/appium.app/downloads/">https://bitbucket.org/appium/appium.app/downloads/</a>

#### Java环境

参考：<a href="http://www.runoob.com/java/java-environment-setup.html">http://www.runoob.com/java/java-environment-setup.html</a>

#### Android环境

也好好。请参考：<a href="http://www.eoeandroid.com/thread-272627-1-1.html">http://www.eoeandroid.com/thread-272627-1-1.html</a>

#### eclipse
后面我会用Java来开发这套脚本，IDE必不可少。<a href="http://www.eclipse.org/">http://www.eclipse.org/</a>

#### 总结
整个开发环境搭建相对比较繁琐，需要足够的耐心。一步步拆解开来，事情就会变得简单起来。其实很多事都是同样的道理，一口气吃不成胖子。

### 具体实现

#### 简单介绍
介绍是为了加深理解。如果不需要深入了解，可忽略。

1. Appium介绍  
	官网：<a href="http://appium.io/">http://appium.io/</a>  
	一篇不错的介绍：<a href="http://www.cnblogs.com/nbkhic/p/3803804.html">http://www.cnblogs.com/nbkhic/p/3803804.html</a>

2. UIAutomatorViewer

	UIAutomatorViewer是androidSDK里面的一个工具，主要用来定位页面上的一些组件。在后面的开发中，需要该工具的协助。

	介绍：<a href="http://blog.csdn.net/chenbang110/article/details/23371731">使用uiautomator做UI测试</a>  
	进阶篇：<a href="https://testerhome.com/topics/2632">uiautomatorviewer二次开发之自动生成控件定位符</a>

#### 简单实现
1. 启动appium  
	启动后需要点击右上角的三角按钮，才算全部启动。

2. 启动uiautomatoreviewer.bat  
	位于%ANDROID_SDK%/tools/bin/uiautomatoreviewer.bat。 双击启动即可。

3. 然后就是写代码了。

	3.1. eclipse中新建工程，导入相应的库文件。  
		Selenum类库：<a href="http://docs.seleniumhq.org/download/">http://docs.seleniumhq.org/download/</a>  
		Appium类库：<a href="http://appium.io/downloads.html">http://appium.io/downloads.html</a>

	3.2. 初始化工作  

		DesiredCapabilities capabilities = new DesiredCapabilities();
		capabilities.setCapability("deviceName","Meizu M1 Note");
		capabilities.setCapability("platformVersion", "5.1");
		capabilities.setCapability("appPackage", "com.tencent.mm");
		capabilities.setCapability("appActivity", ".ui.LauncherUI");
		capabilities.setCapability("unicodeKeyboard", "True");
		capabilities.setCapability("resetKeyboard", "True");
		driver = new AndroidDriver<>(new URL("http://127.0.0.1:4723/wd/hub"), capabilities);

	我用的是魅蓝note手机，操作系统版本5.1。这里需要修改成你的手机信息。

    3.3. 进入附近的人  

		AndroidElement faxian = driver.findElement(By.name("发现"));
		faxian.click();
		AndroidElement fujin = driver.findElement(By.name("附近的人"));
		fujin.click();

	这里相对比较简单。但是在这里还是推荐你简单了解一下AndroidDriver下面的一些API。   
	简单一点的可参考<a href="https://testerhome.com/topics/3144">Appium 中文 Appium API 文档</a>。  
	如果你想深入了解，可参考：<a href="http://appium.io/slate/cn/master/?ruby#">官方文档</a>  
	快速通道：<a href="http://appium.github.io/java-client/">Java API</a>

	尤其是关于一些如何定位一个组件的findElement等方法。
	![findElement](/media/android/findelement.jpg)  
	推荐资料:<a href="http://www.w3school.com.cn/xpath/index.asp">XPath教程</a>

	其实你搞清楚这些findElement方法，后面就基本没啥问题了。

	3.4. 按个进行打招呼。

		List<AndroidElement> lis = driver.findElementsByXPath("//android.widget.ListView/android.widget.LinearLayout");
        int length = lis.size() ;
        for ( int i =0 ;i<length; i++) {
			AndroidElement itemElement = lis.get(i) ;
            String nickName = itemElement.findElementById("com.tencent.mm:id/any").getText().toString() ;
            itemElement.click();
            sendMessage(message);
        }


搞懂findElement方法就相对简单了。找到每个listview的item项，然后发送点击事件进入即可。

3.5. 发送消息。

		private void sendMessage(String message) throws Exception {
			AndroidElement dazhaohu = driver.findElement(By.name("打招呼"));
			dazhaohu.click();
			delay(1);
			AndroidElement et = driver.findElementById("com.tencent.mm:id/ce3");
			et.click();
			et.sendKeys(message);
			AndroidElement send = driver.findElement(By.name("发送"));
			send.click();
		}

3.6. ending  
大致就差不多了。防止一些别有用心的人，所有的源代码，就不开放了。其实项目难度本身不大。
1. 环境搭建的确复杂，但是你只要有足够的耐心，也不存在什么难点。  
2. 深入学习findElement的相关API，配合Uiautomatorviewer.bat，定位一个组件。
3. 这只是简单的demo，能跑起来，但未必能发得出去。微信在这上面做了很多的措施，实际情况肯定没这么简单。玩玩可以，^_^，Just For Fun！。


### 一些坑

1. uiautomatorviewer.bat无法抓取屏幕布局，报错Unable to connect to adb. Check if adb is installed correctly。

	![uiautomatorviewer](/media/android/uiautomatorviewer_err.jpg)

	用记事本打开uiautomatorviewer.bat，将最后一句中的%prog_dir%替换为android SDK/platform-tools的路径。重启即可。

	例：  
	call "%java_exe%" "-Djava.ext.dirs=%javaextdirs%" "-Dcom.android.uiautomator.bindir=%prog_dir%" -jar %jarpath% %*

	替换为  
	call "%java_exe%" "-Djava.ext.dirs=%javaextdirs%" "-Dcom.android.uiautomator.bindir=D:\AndroidSDK\platform-tools" -jar %jarpath% %*  
	其中D:\AndroidSDK\platform-tools是我本地的SDK安装路径。


2. appium中sendKeys时间过长，耗时需要8秒左右。

	可使用开源项目<a href="https://github.com/senzhk/ADBKeyBoard">ADBKeyBoard</a>代替，缩短输入中文的时间。

	上面的et.sendKeys(message)替代为

		Runtime.getRuntime().exec("adb shell ime set com.android.adbkeyboard/.AdbIME");
		Runtime.getRuntime().exec("adb shell am broadcast -a ADB_INPUT_TEXT --es msg ' " + message + "'");

	即可。

3. driver.swipe()方法

	swipe方法最后一个参数，如果设置的过短，滑动事件就变成了点击事件。困扰了很久，╮(╯▽╰)╭，压根就没往这方面考虑。这个就是经验的积累了。

		driver.swipe(100, 1500, 100, 100, 2000);

4. 关于appium是否支持一台设备测试多个APP，APP之间存在关联性。

	答案显然是可以的。你可以通过下面的方法启动另外一个APP。

		driver.startActivity("com.tencent.mm",".ui.LauncherUI", null, null);

	问了一些人，表示并没遇到过这种场景，所以不清楚，甚至通过原理分析认为并不可行。老话说的对，实践是检验真理的唯一标准，差点因为这个听起来很有道理的建议，而放弃这个框架改用按键精灵。当然别人的意见，还是得听听的，但决策需要在综合考量之后才能作出。

5. 快速清空输入框中的内容

	    private void clearText(String text) {
			driver.pressKeyCode(123);
			for (int i = 0; i < text.length(); i++) {
				driver.pressKeyCode(67);
			}
		}

### 总结

不重复造轮子。每个程序员在入行的时候，都会听到这样的一句话。但是博主做了这么久程序员，还是喜欢发明轮子。
有些简单的道理，听起来简单，但做起来却未必如此。  
在该项目过程中，我一直纠结于输入中文的时候，时长较长。不知道是关键字不对，还是相关资料较少，搜索引擎未帮上太多的忙。当时就想着自己通过adb shell录入中文。好在这时，朋友介绍了ADBKeyBoard，解决了我的问题。相比真要做下去，浪费的时间会非常多。  
不妨在搜索引擎上多花点时间，后面可能就会少花很多时间。要相信你遇到的问题，别人也会遇到，也会有相应的解决办法，切勿钻牛角尖。
