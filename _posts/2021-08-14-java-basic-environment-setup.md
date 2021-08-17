---
layout: post
title:  "Java环境配置"
categories: 教程
mathjax: true
---

# Java 环境下载

[JAVA下载地址](https://bell-sw.com/pages/downloads/)
为什么建议下载bellsoft的java开发版本，是因为在java11版本之后，在标准的jdk中移除了JFX，而HMCL需要JFX才能使用，我们可以自己下载JFX来用，但是很多人都设置失败无法使用HMCL。
BELLsoft版本的JAVA是集成JFX功能的，下载之后配置好环境就可以使用了！
建议下载ZIP版本，次版本需要手动设置环境配置，但是方便对版本java共存及随时调整java版本使用。

![image.png](https://i.loli.net/2021/08/17/3CTESGxv5faqkFN.png)

# 配置环境变量

1.
右击“我的电脑”→“属性”→“高级系统设置”→“高级”→“环境变量”；（win10系统可直接在左下角搜索框搜索“编辑系统环境变量”）

![image.png](https://i.loli.net/2021/08/17/hgco9ZJAUQjtIpn.png)

![image.png](https://i.loli.net/2021/08/17/WSz1n4aEc8sfGNH.png)

2.
选择"高级"选项卡，点击"环境变量"；

![image.png](https://i.loli.net/2021/08/17/b4AayfZIPo1WXBn.png)

3.
新建“JAVA_HOME”系统变量（点击“系统变量”下方的“新建”按钮，填写变量名与变量值，点击“确定”）

![image.png](https://i.loli.net/2021/08/17/BVNfwRGtsjQukDc.png)

4. 同上，新建“CLASSPATH”系统变量，变量值为“.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;”。（引号内的全部内容，注意最前方是点不是逗号）

![image.png](https://i.loli.net/2021/08/17/Wti3mPVqzZE9JHe.png)

5.
双击“系统变量”下的“Path”变量进行编辑。（此时可以看到JAVA_HOME已经存在于系统变量中），（有的电脑"Path"也写作“PATH”）

![image.png](https://i.loli.net/2021/08/17/fNqID2yh9SVoYgl.png)

* **Windows7/8**：
在变量值末尾输入;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin 。注意要用英文分号;分割：

![image.png](https://i.loli.net/2021/08/17/B2DT4y7SxVXhU5P.png)

* **Windows10**：

![image.png](https://i.loli.net/2021/08/17/F7q1pyJdBIg8S9o.png)

点击新建，在里面分别输入

![image.png](https://i.loli.net/2021/08/17/iwKFsyz1vuHa79L.png)

在"系统变量"中设置3项属性，JAVA_HOME,PATH,CLASSPATH(大小写无所谓),若已存在则点击"编辑"，不存在则点击"新建"。

变量设置参数如下：
```sh
* 变量名：JAVA_HOME
* 变量值：C:\Program Files (x86)\Java\jdk1.8.0_91        // 要根据自己的实际路径配置

* 变量名（1.5以上版本，此项无须设置）：CLASSPATH
* 变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;         //记得前面有个"."

* 变量名：Path
* 变量值：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```
# 通过控制台测试JDK是否安装成功
1、同时按键盘上“win”、“R”两个键打开运行，输入“cmd”确定打开控制台。

![image.png](https://i.loli.net/2021/08/17/xiAY2Z8wyRX57GL.png)

2、键入命令: java -version、java、javac 几个命令，出现以下信息，说明环境变量配置成功；

![image.png](https://i.loli.net/2021/08/17/RvSxlCpdiu3M7WJ.png)

# Linux，UNIX，Solaris，FreeBSD环境变量设置
环境变量PATH应该设定为指向Java二进制文件安装的位置。如果设置遇到困难，请参考shell文档。

例如，假设你使用bash作为shell，你可以把下面的内容添加到你的 .bashrc文件结尾: export PATH=/path/to/java:$PATH
