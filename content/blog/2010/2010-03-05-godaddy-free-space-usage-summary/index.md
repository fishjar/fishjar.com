---
author: yugang2002
comments: true
date: 2010-03-05 19:14:00+00:00
layout: post
link: https://fishjar.wordpress.com/2010/03/06/godaddy%e5%85%8d%e8%b4%b9%e7%a9%ba%e9%97%b4%e4%bd%bf%e7%94%a8%e5%b0%8f%e7%bb%93/
slug: 'godaddy-free-space-usage-summary'
title: Godaddy免费空间使用小结
wordpress_id: 8
---

Godaddy（[www.godaddy.com](http://www.godaddy.com/)）是全球最大的域名注册商，自从我放弃国内的cn域名之后，现在所有域名都已经是在Gdaddy注册的了。Godaddy相比国内注册商的各种优点这里就不多提了，想提醒一下的就是现在已经可以用支付宝支付了，很方便哦。

在Godaddy注册的域名都会附送一个10G的免费空间，而且还支持创建10个MySQL数据库。Godaddy免费空间是需要自己开通的，至于如何开通，比较简单，网上也有不少教程，这里就不多讲了，我就简单介绍下使用该免费空间的一些小结。

**1、绑定域名的一个小技巧**

开通免费空间的时候，Godaddy会自动帮你修改好DNS设置，增加一条a记录到服务器IP。

但如此的话会产生一个问题，就是比如你的域名是abc.com，那么只能访问http://abc.com ，而不是像我们的习惯访问 http://www.abc.com 。当然很快会想到，再增加一条cnames记录将www指向服务器不就行了，还存在的问题就是网站将变成2套网址了，比如访问http://abc.com和http://www.abc.com都可以，而且都是同一个页面，不仅郁闷自己，也给搜索引擎带来郁闷。

现在需要解决的就是访问http://abc.com是自动跳转到http://www.abc.com，按照常规的想法有一个解决办法就是，将www直接做a记录到服务器，然后再利用域名转发将abc.com跳转到www.abc.com，但可惜的是Godaddy不支持域名转发，记得以前DNSPOD支持过一段时间转发，后来也取消这个服务了，所以这条路行不通。

另一个解决办法，就是利用.htaccess文件。新建一个.htaccess文件，里面写上如下代码：
    
    #abc.com是例子，需要换成你自己的域名
    RewriteEngine On
    RewriteCond %{HTTP_HOST} ^abc.com [NC]
    RewriteRule ^(.*)$ http://www.abc.com/$1 [L,R=301]




然后上传到服务器主目录，这样基本完美实现了访问访问http://abc.com自动跳转到http://www.abc.com，问题解决！




需要注意的是windows系统可能无法直接创建.htaccess文件，办法就是上网下载一个，然后用记事本打开来修改内容。




**2、一个空间建立多个站点**




10G的免费空间对于小型的个人网站来说是绰绰有余的，如果不想浪费可以多绑定几个域名，实现建立多个站点。具体方法如下：




首先当然是把你要建立新站点的域名绑定到免费空间上，然后修改域名的DNS指向该空间，接着在主目录下建立一个新目录，目录名随便起。




接着还是利用.htaccess文件，用记事本打开后，输入如下代码：
    
    # 把 yourdomain.com 改为你要绑定的域名.
    RewriteCond %{HTTP_HOST} ^(www.)?yourdomain.com$
    
    # 把 subfolder 改为要绑定的目录.
    RewriteCond %{REQUEST_URI} !^/subfolder/
    
    # 不要改以下两行.
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    
    # 把 subfolder 改为要绑定的目录.
    RewriteRule ^(.*)$ /subfolder/$1
    
    # 把 yourdomain.com 改为你要绑定的域名
    # 把 subfolder 改为要绑定的目录.
    # subfolder/ 后面是首页文件index.php, index.html......
    RewriteCond %{HTTP_HOST} ^(www.)?yourdomain.com$
    RewriteRule ^(/)?$ subfolder/ [L]




然后上传到服务器，这样当访问yourdomain.com时，会自动跳转到abc.com的subfolder目录下，浏览器地址栏却依然是显示yourdomain.com。当然，如果你希望新建的站点应该用www.yourdomain.com来访问的话，仿照此文章第一条技巧即可。




按照上面的方法，只要你愿意，一个免费空间理论上是可以建立无限多个站点的，是不是很爽啊。爽归爽，下面麻烦就来了。




**3、去除广告**




Godaddy不是傻子，所以这个免费空间不是纯净空间，而是带广告的，而且这个广告还比较流氓，非常大，会显示在所有页面顶端，于是我们就得想想如何去掉这个广告。




关于这个广告，有些人说如果私自去掉空间会被封的，但好像并无有人报告先例，所以就权且低调点的去掉吧。




其实想真正去掉此广告是比较有难度的，真正简单可行的办法就是利用CSS隐藏掉，一句代码解决：
    
    #conash3D0 {
    	display: none;
    }




或者另外一句代码也可以解决，但没有上面的好，display和visibility是有区别的，了解CSS的应该知道。
    
    #conash3D0 {
    	visibility: hidden ;
    }




**4、搭建wordpress站点**




前面说过此免费空间支持MySQL数据库，因此也就能用来搭建现在流行的wordpress站点了，至于如何搭建就不多讲了，这里只讲讲搭建好之后的问题，基本上问题都是来自于Goaddy的JS广告，而且产生的问题多多。




首先的问题就是前台广告，这个好办，利用上面的技巧，修改模板里的CSS文件即可。




然后是后台，不仅有广告，而且界面凌乱，[这里](http://wordpress.org/support/topic/280162)有个帖子介绍了解决办法，写得很详细了，我再复述一下：




在wordpress的wp-includes目录下找到script-loader.php文件并打开，大概第619行的地方，函数function _print_scripts()里面有如下代码：




echo "<script type='text/javascript' src='" . esc_attr($src) . "'></script>\n";




替换成：




// Clean for godaddy free hosting   
$st_data = file_get_contents( $src );   
$st_data = substr( $st_data, 0, strrpos( $st_data, '</iframe>' ) );   
echo "<script type='text/javascript'>\n";   
echo $st_data;   
echo "</script>\n";




第686行，函数print_admin_styles()的如下代码：




echo "<link rel='stylesheet' href='" . esc_attr($href) . "' type='text/css' media='all' />\n";




替换成：




// Clean for godaddy free hosting   
$st_data = file_get_contents( $href );   
$st_data = substr( $st_data, 0, strrpos( $st_data, '</iframe>' ) );   
echo "<style type='text/css' media='all'>\n";   
echo $st_data;   
echo "</style>\n";




后台界面可以正常载入CSS了，但广告问题还存在，进一步修改wp-admin目录下的admin-footer.php文件，在
    
    <code></body>
    </html></code>

前面加入代码 
    
    <code><script type="text/javascript">
      // ADS godaddy son's 1 per plugin in dashboard
      function hideGoDaddy(){
        var bd = document.body;
        if( bd.childNodes.length > 0 ) {
          for( j = 0; j < bd.childNodes.length; j++ ) {
            if( 'IFRAME' === bd.childNodes[j].nodeName ) {
    			 gDaddy = bd.childNodes[j];
              if( 'conash3D0' === gDaddy.id ) {
                if( 'none' === gDaddy.style.display ) {}
                else { gDaddy.style.display = 'none'; }
              }
            }
          }
        }
      }
      setInterval("hideGoDaddy()", 2000);
    </script></code>




至此，后台界面问题基本解决，但还是有些小问题，比如编辑器无法使用之类，这倒好解决，使用其他发布方式即可，比如我，就用Windows Live Writer来发布。




一些其他问题，比如[不能回复评论、后台无法添加标签](http://bbs.idcspy.com/thread-54050-1-1.html)、[feed出错的问题](http://blog.happysa.org/blog/solve_the_feed_problem_of_godaddy.html)、插入[Yahoo的JS音乐播放器](http://mediaplayer.yahoo.com/)无法正常播放，我认为无关紧要，也就没去研究解决了。[这里](http://fivebig.com/blog/2010/01/wordpress-on-godaddy/)也有篇汇总性的文章，有兴趣可以看看。




还有个比较致命的问题，我退出登录后就再也不能登陆后台，一时也找不到解决方法，幸好我是用Windows Live Writer来发布文章，不用经常性登录后台。网上有人说清空临时文件就可以了，但我试过还是不行，有时间再研究下。谁知道如何解决的话麻烦告知，先谢了。




![](http://fishjar.files.wordpress.com/2010/03/a987789f36904bdf68985ac5ea8f494f.jpg?w=300)
    
    周传雄-短消息
    专辑:蓝色土耳其
    
    还是留着你寄来的短消息
    总是眷惜舍不得删去
    因为这是如今和你
    唯一留下来的关系
    我靠这样沉溺继续着呼吸
    
    有些事情你永远不会忘记
    我猜这就是刻骨铭心
    有谁听到我的叹息
    夜深人静时的哭泣
    这寂寞空气多令人窒息
    
    我多么爱你又多么想你
    热恋时你send的永恒咒语
    忍不篆寻痛苦又甜蜜
    像刀子般一刀刀割在心里
    
    感动的回忆相似的字句
    生日祝福生动的灼伤眼睛
    明明想忘记却藏着痕迹
    多么痛恨这样矛盾的情绪
    
    谁愿帮我从这深渊里逃离

 
