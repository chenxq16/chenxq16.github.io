---
layout: post
title:  SublimeText中Package Control的安装
date:   2016-05-29 13:10:11
tags:
    - other
---

sublime安装Package Control。

---

请参考：[https://packagecontrol.io/installation#st2](https://packagecontrol.io/installation#st2)


### 在线安装
使用Ctrl+`快捷键或者通过View->Show Console菜单打开命令行，粘贴如下代码：

```
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

```
此时就可以在Preferences菜单下看到Package Settings和Package Control两个菜单了。

Sublime Text2 的代码：

```
import urllib2,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); os.makedirs( ipp ) if not os.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener( urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open( os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Error validating download (got %s instead of %s), please try manual install' % (dh, h) if dh != h else 'Please restart Sublime Text to finish installation')
```
### 手动安装Package Control：
1.点击Preferences > Browse Packages菜单
2.进入打开的目录的上层目录，然后再进入Installed Packages/目录
3.下载<a href="https://packagecontrol.io/Package%20Control.sublime-package">Package Control.sublime-package</a>并复制到Installed Packages/目录
4.重启Sublime Text。

### 汉化
1、快捷键<strong>ctrl+shift+p</strong>,打开Package Control
2、输入pci，选择 <strong>Package Control:Install package</strong>
3、输入chinese,选择ChineseLocalization，等待安装，即可。
