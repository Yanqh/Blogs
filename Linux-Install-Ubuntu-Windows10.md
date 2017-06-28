---
title: Windows10和Ubuntu16.04双系统
date: 2017-06-14 12:10:49
tags: 
- Linux
- Ubuntu
category: Linux
comments: true
---

这几天安装了**Windows 10**和**Ubuntu16.04**的双系统，先在虽然已经有了很多的教程，但是在装的时候还是遇到了很多教程上没有的坑，记录一下，可能不是很准确，希望会有帮助。

<!-- more -->

先贴上一个比较完整的教程，对照着教程和我的记录来安装，应该不会有什么问题，我已经安装了很多次了。教程点击这里：[Windows10+Ubuntu双系统安装](http://www.jianshu.com/p/2eebd6ad284d)

#### 坑1：找不到预留好的磁盘

按照教程上的说法，我从U盘启动之后在安装的时候会自动检查到未分配的磁盘空间，但是我却没有看见，一顿搜索，发现我的磁盘分区是动态的，必须要使用基本分区才能被**Ubuntu**搜索到，于是我下载了[傲梅分区助手](http://www.aomeikeji.com)，然后进行磁盘的分区，把动态的磁盘转换成基本的，这样就可以看见了。

{% alert danger %}
这里一定要做数据的备份，我当时没有做，差点把结婚的照片都搞没了，一定要备份。
{% endalert %}

#### 坑2：进入U盘安装之后，Logo也出现了，就是进不去

这里按任意键可以看到启动的细节，这里会看到一行代码老是出现{% hl_text red %}
    /init:line7:cant open /dev/sr0
    {% endhl_text %}
我卡在这里有很长一段时间，因为说法有两种，但是最会使用了一个连我自己都觉得不会成功的方法成功了，看来网友是不会骗人的。具体是这样做的：

1. 在Window系统下打开U盘的文件，找到{% hl_text success %}
    /boot/grub.cfg
    {% endhl_text %}用编辑器打开
    
2. 使用下面的代码全部替换：
    ```
    title Ubuntu 16.04
    find --set-root /path-to-casper/casper/initrd.lz
    kernel /path-to-casper/casper/vmlinuz --ignore-floppies cdrom-detect/try- 
    usb=true live-media-path=/path-to-casper/casper/ ignore_uuid boot=casper --      
    lang=us
    initrd /path-to-casper/casper/initrd.lz
    ```
3. 从U盘启动，到了Logo界面，按F6，会发现上面的代码显示了，并说没有找到。
4. 这时候拔出U盘，再重新插上，重启安装就可以了。



#### 坑3：选择了 / 和 swap 之后未分配的磁盘分区出现不可用

这是因为主分区过多了，其实不用按照教程中的需要使用主分区，可以都放在逻辑分区。


#### 坑4：出现无法卸载挂载点

在点击了继续之后可能会出现 {% hl_text danger %}
卸载分区失败， 安装程序需要更改分区表，但未能实现，因为不能卸载以下挂载点 /文件名
{% endhl_text %} 。我出现这个时候没有看后边的文件名，通过搜索网上的方法，直接在命令行使用了命令，但出现了不能找到（can't find /isodevice）,后来发现是我的文件名不是这个，而是 {% hl_text success %}
/cdrom
{% endhl_text %}，果断更改命令为：

```
sudo umount -l /cdrom
```
这样就不会在出现卸载分区失败了。

#### 坑5：grub2出现问题

这里是我最最浪费时间的一步，总是找不到解决的方法，最后也是最想不到的方法解决了：联网！！之前没有想到是因为我每次跑进度的时候都出去抽烟了，然后回来看的时候屏保出现了，就这样我的网络断了。。。安装的时候一定要联网！！

#### 坑6：重启后的黑屏

这里是我后来才发现的，因为之前安转好之后就没有重启过，所以没遇到这个问题，后来重启了一次才发现，当Logo界面出现后就黑屏了，原以为是我删错了东西的原因，后来重装了一次（真是悲催），但是还是遇到了一样的问题，最后搜索发现了解决的方法，这是显卡驱动的问题。
开机进入**grub**界面按**e**，在以**linux**开头的一行，**quiet**后添加**nomodeset**，按**F10**重启。
然后会进入图形化界面，去设置-更新-驱动，然后安装最新驱动就好了。

#### 最后说一下 EasyBCD
这里的类型要选择 {% hl_text success %}grub2{% endhl_text %}，这样就成功了。装好了之后挺开心的，但是新的问题也出现了：主题美化、软件安装、命令使用....加油！！


