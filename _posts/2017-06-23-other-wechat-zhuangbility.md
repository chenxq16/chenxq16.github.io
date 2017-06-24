---
layout: post
title:  朋友圈装逼利器
subtitle:   "足不出户游全球"
author: "MakerChen"
# header-img: ""
date:   2017-06-23 15:59:00
tags:
    - other
---

### 朋友圈装逼利器-足不出户游全球

#### 背景
受友人所托，需要在微信上模拟位置，搜索附近的人，然后。。。

不管怎样，搞技术的，就搞搞吧。随口就答应了，本以为会搞不出来。潜意识认为微信会对hookAPI有所保护，事实说明过于高估了。至少对定位逻辑缺少保护，然后嘿嘿嘿～～～

#### 准备工作
这种类型的文章需要关注一下其实效，随着技术的演进，现在的方法未必适用于未来。Mark一下。

时间：2017-06-24  
微信版本：6.5.8

其他条件：一台闲置的android手机；会root、能刷机。

#### 原理
利用强大的xPosed框架，实现对底层微信定位逻辑代码的Hook，伪装GPS坐标，将伪装过的GPS坐标信息返回给微信实现模拟定位。让你分分钟，来回于上海-拉萨，实现装逼的目的。

#### 步骤
1. <b>清理你的手机并充满电。</b>
	* 由于会用到xPosed、需要root，为了保证你手机上的信息相对安全，需要清理敏感资料，卸载各种金融类APP（支付宝、证券银行类APP，绝不能买的P2P类APP等）  
	温馨提示：别光顾着装逼，丢了钱财、遗失艳照等。发生上述事件，概不负责。<br/><br/>
2. <b>请检查第一步，很重要。</b>  
博主面试过很多P2P平台的技术人员，其安全意识非常薄弱。各种账号、密码，明文传、明文存，简直不敢想象。真要作死买P2P的，一定要在不同的平台设置成不同的密码。<br/><br/>
3. 唠叨一下，回归正题。<b>清理完手机后，你需要把手机root掉。</b>  
	怎么root？你不会就不要往下看了，也不展开了，教程一大堆。推荐个软件KingRoot。<br/><br/>
4. 重要的一步。Xposed框架可能会导致手机变砖。<b>可提前将手机的rom包update.zip，拷贝至手机SD卡的根目录下</b> 。避免因为“伪砖”，无法恢复。当然真砖，那就没办法了。装逼有风险，想清楚了再往下。<br/><br/>
5. <b>安装Xposed。</b>官网 <a href="http://repo.xposed.info/">http://repo.xposed.info/</a> ，找到适合你手机的版本。博主是魅蓝Note android 5.1版本，所以下载的 <a href="http://forum.xda-developers.com/showthread.php?t=3034811 ">http://forum.xda-developers.com/showthread.php?t=3034811 </a>4.4以下系统的请下载 <a href="http://dl-xda.xposed.info/modules/de.robv.android.xposed.installer_v33_36570c.apk">http://dl-xda.xposed.info/modules/de.robv.android.xposed.installer_v33_36570c.apk</a> 。Xposed框架对有些手机是不支持的。<br/><br/>
6. Xposed框架安装过程中，会反复的重启设备，需要耐心等待。当然，也有可能就启动不起来了。幸运的话，只是变成了“伪砖”。这时前面提前放置进去的rom包就起到了很大的作用。利用音量+和开机键，进入recovery模式，实现卡刷，恢复系统。不幸的话，那就只能拿来砌墙了。<br/><br/>
7. 能看到这里，说明一切还挺顺利。  
	* 打开KingRoot，给Xposed Installler授予root权限。
	* 进入开发者选项中，打开USB调试、打开模拟位置。
	* 进入定位服务，修改仅使用GPS定位。<br/><br/>
8. 打开APP， XPosed Installer框架中，搜索FakeLocation。然后下载-安装-重启设备。  
	* 之前博主使用的是FakeMap，但目前的版本，会导致手机自动重启并且无法进入手机，期待作者的修复。目前推荐FakeLocation，版本1.2.681.1701，亲测可用。<br/><br/>
9. 重启完成后，桌面上会多一个APP-模拟位置。打开模拟位置APP，如下图  
![图1](/media/wechat/wechat-zhuangbilitiy-01.jpg)
选择微信APP，选择右边的地图按钮，想在那就去那。选完位置后，点击更新。等到提示已更新。  
![图2](/media/wechat/wechat-zhuangbilitiy-02.jpg)
打开微信APP，切换到发现，选择好事先准备好的图。选择所在位置，你会发现。WOW，我已经在拉萨了。  
![图3](/media/wechat/wechat-zhuangbilitiy-03.jpg)<br/><br/>
10. 装逼完成，等你朋友来赞吧。  
![图4](/media/wechat/wechat-zhuangbilitiy-04.jpg)


#### 问题
1. FakeLocation一次模拟位置，只能装逼一次。
2. 注意风险。
3. <b>还不关注、打赏，想啥呢～～～</b>
