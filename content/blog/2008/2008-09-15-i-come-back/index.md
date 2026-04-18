---
author: yugang2002
comments: true
date: 2008-09-15 21:21:03+00:00
layout: post
link: https://fishjar.wordpress.com/2008/09/16/i-come-back/
slug: i-come-back
title: I come back
wordpress_id: 9
---

好久好久没有写blog了，我也苦笑着问自己，为什么会这样，真的很久了。 

太多的原因就不深究了，只说说其中一点吧，就是我原来使用线下发布工具zoundry来更新blog的，但有一天我发现不能用了，一直未能找到替代的发布工具。后来找到Zoundry Raven来用，据说这是zoundry的新版本，但也是不行。再后来，就发现大家都说live writer不错，于是找来试用，提示服务器出错，真是扫兴，我都打算开始更新blog了，竟然找个发布工具都找不到。

不甘心就此罢休的我在网络上查找live wrtier出错的原因，花了许多时间也都无功而返。有人说是XP系统的问题，需要安装SP3，于是我安装了SP3，可是仍然出错。有人说是墙的问题，于是我下载安装了tor，然后设置代理，也无济于事。我一直怀疑的是账户设置问题，但所有的设置我都尝试过，问题依旧。

几近绝望的时候，我甚至痛恨起这个垃圾信息充斥的互联网来，因为我在网上看到无数的人都说live writer支持blogger，但就是不说如何设置，有些说了的，我按照他们的方法都不行。不过也有个可能，就是我的blogger是设置自定义域名，而且是ftp发布的，可能有点特殊。

黄天不负有心人，一个偶然我发现[guxq](http://guxq.cn/)有和我一样的情况，也是自定义域名ftp发布，发现该blogger有用live writer。于是咨询博主，这才算是真相大白。搞来搞去，就是墙的问题，万恶的墙，我除了无语，能做什么，fuck一万遍也难解心头恨。

废话不多讲了，下面我就简要写个小教程：live writer 发布 blogger

1、准备软件：

<blockquote>1）live writer ，下载地址：[http://get.live.com/writer/overview](http://get.live.com/writer/overview)
> 
>  
> 
> <strike>2）破墙工具，下载地址：[http://www.box.net/shared/qx11nat9ix](http://www.box.net/shared/qx11nat9ix)</strike>（版本失效）
> 
> 2）破墙工具，下载地址：[http://fy.k.vu](http://fy.k.vu)
> 
> </blockquote>

2、<strike>第二个软件是绿色软件，下载后解压到任意文件夹，双击运行，运行后会自动启动IE，不用管它。当然关掉IE也可以，不要关破墙软件就行。</strike>破墙软件包中包含了多款软件，解压后，使用其中的任何一款即可。

3、安装 live writer ，到了添加账户时，选择其他日志服务，如下图：

[![](http://farm4.static.flickr.com/3179/2860769322_6c42e3a694_m.jpg)](http://www.flickr.com/photos/yugang2002/2860769322/)

4、填写日志主页、用户名和密码。日志主页就写你blogger的主页地址，记得用户名那里不用加@gmail.com。如下图：

[![](http://farm4.static.flickr.com/3029/2860769450_44628edddb_m.jpg)](http://www.flickr.com/photos/yugang2002/2860769450/)

5、接着填写日志类型和远程发布url。日志类型选择Blogger，远程发布类型有个默认的链接，把其中<feed-id>换成自己的blogger里的ID。如下图：

[![](http://farm4.static.flickr.com/3206/2859941929_e6062c1cc2_m.jpg)](http://www.flickr.com/photos/yugang2002/2859941929/)

有些人可能不知道自己blogger的ID是怎么回事，其实很简单，登录blogger后，点击设置，你再看看浏览器地址栏，上面能显示你的ID，如下图：

[![](http://farm4.static.flickr.com/3290/2859941991_09f7f26b22_m.jpg)](http://www.flickr.com/photos/yugang2002/2859941991/)

6、点击下一步之后稍等一会，不出意外的话 live writer 会提示你发布一篇测试的日志，这就意味着大功告成了。

是不是很简单？实际上最关键的一步就是第二步的运行破墙软件，其他步骤应该大部分人都会的。

有几点要说明的是：

1、<strike>我没有排除SP3对live write正常使用是否有影响，因为我测试成功是在安装了SP3之后的，所以如果你按照我的方法还出错，不排除是没有安装SP3。</strike>

2、这个破墙软件的<strike>版本</strike>不保证一直有效，<strike>但好像这个软件可以自动升级的，所以如果你要保持有效的话，最好下载后隔一段时间运行一次，让它自动升级。</strike>，因为这个地址是他人提供的。

3、<strike>至于能否用其他破墙软件代替，我也不清楚。只知道知名度很大，使用甚广的tor不行，我已经试过无数次。</strike>也可以用其他的破墙工具代替上面下载的破墙软件，具体不在此描述了，关键的地方在于，不要在live writer软件中设置代理，只需在IE浏览器的Internet选项中设置好代理即可，可以通过IE上网测试代理是否设置成功。

4、如果你发现这个方法真的不行了，请告诉我，我会第一时间修改或删掉这篇文章，我不想在信息过载的互联网留下过时文章，因为我自己也深受其害。

5、再次谢谢[guxq](http://guxq.cn/)。

6、live writer软件好像更新过了，上面添加账户步骤可能有变，并且如果不是自定义域名且FTP发布的步骤也会不同，但都无所谓，只要知道了翻墙，live writer应该大家都会操作的了，我就不重复去测试并截图了。

最后更新时间：2010年2月3日。
    
    <strong>我还不想睡</strong>
    MP3听了好几个轮回这样的夜无法入睡桌上残留隔夜的咖啡还有挥之不去的烟味我以为自己不懂得伤悲这样的夜情不自禁流泪爱上了你 爱上了心碎窗帘也嘲笑我的狼狈天那么黑 无路可退我不知道我在等着谁心那么累 已无所谓幸福是沙堆 摇摇欲坠我还不想睡任凭风在吹就算我已经感到万分疲惫我还不想睡思绪已枯萎这样的夜让我一个人醉
