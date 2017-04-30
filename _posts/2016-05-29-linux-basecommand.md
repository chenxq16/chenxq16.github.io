---
layout: post
title:  Linux学习－基本命令
date:   2016-05-29 13:15:11
tags:
   - linux
---

Linux学习笔记

---

---

### 文件命名规则
除了／之外，都合法。
避免使用一些特殊字符
避免使用.作为普通文件名的第一个字符
大小写敏感

### 命令格式
命令 -选项 参数

| drwxr-xr-x    | 2   | root | root   | 4096  |   
| :--------: |:----:| :-----:|:-----:|:-----:|
|      | 硬链接数  | 所有者 |所属者 |数据块 |


文件类型:  

    d 目录directory
    - 二进制文件
    l 软链接文件link

| rwx        | r-x           | r-x  |
| :--------: |:-------------:| :-----:|
| 所有者u      | 所属组g  | 其他人o |
| user /owner     | group      |   others |

| 代表字符        | 权限           | for文件  |for目录  |
| :--------: |:-------------:| :-----:|:-----:|
| r      | 读 | 查看文件内容 |列出目录中的内容 |
| w     | 写      |   修改文件内容 |可创建、删除文件 |
| x     | 可执行      |   执行文件 |可以进入目录 |

### 命令
#### 查看文件内容
* cat  适合小文件
* more  （空格／f 下一页 ） （回车  下一行） （q 退出）
* head (-num 文件前几行)
* tail ( -num 文件后几行 ) （-f 动态更新）

#### 权限管理内容
chmod ［uga］［+-=］[rwx]  /［mode/777］
chown [用户][文件和目录] (改变文件的所有者)
chgrp [所属组][文件和目录]
umask 默认权限 (缺省创建的文件不能授权可执行文件)

#### 文件搜索命令
which   显示系统命令所在目录,及别名信息
whereis 显示系统命令所在目录,及帮助文档
find [路径][关键字] 查找文件或目录

－－－name、size、user、{ctime、atime、mtime:天}、｛cmin、amin、mmin:分钟｝{c:change［文件属性被修改过］｝［a:access、m:modify[文件内容被修改过] && -a and 、 -o or }］

find /etc -name inittab -exec ls -l {} \;  

locate 从目录文件的数据库搜索文件，比find块，需配合locate使用
updatedb 更新文件数据库
grep 在文件中搜寻字符串匹配行并输出 ［指定字符串］［源文件］

#### 帮助命令
man 帮助命令[命令、配置文件/5]
info 帮助命令
whatis 命令的简要描述 ［makewhatis］
apropos = man -k
help 查看shell内置命令的帮助

#### 压缩解压命令
gzip 只能压缩文件、不能压缩目录。不保留原文件
gunzip = gzip -d
tar 打包目录 （z压缩、c产生.tar文件、v、f文件名、x解包）
zip 默认windows和linux可共享的压缩文件 （－r 压缩目录）
unzip 解压
bzip2 (-k 压缩并保留原文件)
bunzip2 (-k 解压缩并保留原文件)

#### 网络通信指令
write 向另外一个用户发信息 ctrl+d 结束
wall == write all
ping 测试网络连通性
ifconfig 查看网络设置信息

#### 关机、重启
shutdown
reboot

#### bash技巧
TAB键补齐文件名
clear = ctrl + l
ctrl + u 删除光标前所有的字符
alias copy=cp
alias xrm="rm -r"
unalias copy

#### 输入输出重定向
\> 输出重定向 (>>追加)
< 输入重定向
2> 错误输出重定向

#### 管道
| 将一个命令的输出传送給另一个命令，作为另一个命令的输入

#### 命令连接符
； 顺序执行命令
&& 只有&&前面的命令执行了，后面的命令才会执行
|| 只有||前面的命令执行失败后，后面的命令才会被执行

#### 命令替换符 `
示例：ls -l \`whick touch\`
