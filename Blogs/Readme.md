# 好文推荐

## 利用Docker搭建一个自己的专属邮件服务器
![](https://github.com/Eddlys/png/raw/main/cool/45DD36A9C4D888D5F4AC43D3A905D19E.jpg)


大家好啊，好久没写教程了，这次想写一篇关于邮件服务器的教程。 
楼主发现酷安这里好像没有相关的教程，这到底是为毛？难道是因为邮件服务器比较硬核，涉及的知识点较多吗？显然不是。
就个人使用而言，自己搭建邮件服务器似乎意义并不大，毕竟互联网上大厂的邮箱这么多，没必要自己折腾。
那么自己搭建邮件服务器跟网上注册的邮箱相比，到底有啥好处呢？楼主能想到的有以下几点：

1.学习，了解邮件服务器的运作原理。 
2.装比，专属个性的域名后缀和用户名。 
3.隐私，隐秘性强，不涉及任何个人资料，无需手机号和实名。 
4.无限制，无广告，无限个账号，自己的服务器自己做主，爱咋咋地。 
5.可撸羊毛，网上注册账号等。 
6.投名状，如果你是计算机专业的学生，可把自建邮件服务器作为投名状纳给面试公司（这是楼主以前亲身经历，面试题就是搭建邮件服务器）。 
.....

在这里有的人可能会想到使用域名邮箱，其实域名邮箱跟这个不是一回事，域名邮箱只是单纯的把域名解析到厂商的服务器，其他的还是受限于厂商，局限性很大。

在开始之前，还是有必要说下邮件服务器涉及的主要协议和端口如下： 
发件服务：smtp（25）
收件服务：pop3（110）
收件服务：imap（143）
pop3和imap区别是：imap是交互式的，支持同步，也是现在主流的。还有些ssl（tls）加密的就不说了，省些篇幅。

本来啊都已经开始教程的了，然而又出现了幺蛾子。由于楼主使用docker的机器是甲骨文arm手臂服务器，在网上找了一堆开源且优秀的项目，结果全都不支持手臂。 
哇透它猴子，这教程还能不能写了？楼主技术菜，又不会自己编译，这就是典型的又菜又想玩，真叫人头大。没办法，只能到docker官方hub去找找咯。
没想到这个没水的坑真找到了一个叫“码头工人邮件服务器”的镜像，还支持手臂，此镜像集成了linux著名的postfix邮件核心，部署的人还不少。

![支持手臂](https://github.com/Eddlys/png/raw/main/cool/c041736fbfa2f20435ff4d1a61b9216a.jpg)

不过缺点是，没有集成webmail客户端和管理端ui界面，添加账号等只能命令操作。但也比较轻量化，客户端可以使用qq邮箱解决，凑合用吧！
———————————————————— 
需要准备的东西： 
服务器一台，推荐甲骨文ARM麒麟臂4+24，此乃神器，配置高，你可以运行1000个容器（不怕屎的话）。
域名一枚，建议使用非免费域名，以免被人鄙视。楼主使用freenom域名，测试下这个互联网世界，到底还有没有公平正义。

————————————————————— 
一，docker安装 
额，这个没什么好说的，手动安装或者脚本安装都可以，已经安装完成了。
————————————————————— 
二，docker-compose 
这是什么鬼？一个容器编排工具，一些简单的容器我们可以使用docker run命令运行。 
但有些较为复杂，参数较多的容器，比如这个邮件服务器，我们需要用到docker-compose解决。 
安装较为简单，直接复制以下命令

![](https://github.com/Eddlys/png/raw/main/cool/a3cb5495fc8506a72c58e529951a0b8d.jpg)



显示出版本号，即为成功。

![成功](https://github.com/Eddlys/png/raw/main/cool/106ba013987f502df7db9308766e37e7.jpg)


————————————————————— 


三，启动码头工人邮件服务器 
在任意位置新建一个目录并且记住，后续的命令必须要在这个目录下执行，这个目录将存放容器的所有数据。
cd进这个目录，执行下面命令下载容器启动所需的文件

![](https://github.com/Eddlys/png/raw/main/cool/cf321b554875cb3c7b9a4ddc060a4378.jpg)

vim编辑docker-compose. yml这个文件，需要注意的地方有四个。
容器名，不要改。
主机名，不要改。 
域名，改为你自己的。
配置文件，已在当前目录，老手可以改里面的参数。保存。

![yml文件](https://github.com/Eddlys/png/raw/main/cool/e0c11ac437a675caabe8e0f49ef714e8.jpg)

执行下面命令启动容器并且新建账号并且生成dkim密钥，账号域名密码"****""改为你自己的。
后续添加账号只需单独执行第二行。

![](https://github.com/Eddlys/png/raw/main/cool/6.jpg)

在当前目录下找到这个txt文件"/docker-data/dms/config/opendkim/keys/你的域名/mail. txt"。
打开它，把括号这里面的内容复制出来，去掉所有的双引号，p那一行开始去掉换行，让其首尾相连。后续配置dns要用哈。

![txt文件](https://github.com/Eddlys/png/raw/main/cool/7.jpg)

容器启动后可以看到服务器起了一堆服务，不必惊慌，正常现象，我们真正用到的只有smtp和imap。

![龟壳](https://github.com/Eddlys/png/raw/main/cool/8.jpg)

所以，必须到乌龟壳的网页安全规则里放行25和143端口。

![龟壳后台安全列表](https://github.com/Eddlys/png/raw/main/cool/9.jpg)

————————————————————— 


四，DNS配置 
本想研究一下甲骨文的反向区域解析，可没想到 上方出现付费账号提示，楼主胆小害怕出账单，所以只能放弃。

![](https://github.com/Eddlys/png/raw/main/cool/10.jpg)

没办法，只能配置正向解析，这里主要以CF为例，其他的dns都差不多。

![](https://github.com/Eddlys/png/raw/main/cool/11.jpg)

从上到下分别给大家说明一下： 
A记录，主机名mail，指向服务器ip，容器的yml文件有说明，很好理解。
A记录，这个是楼主的博客，跟本文无关，文中的命令都在里面。
别名记录，imap收件解析，客户端用。
别名记录，smtp发件解析，客户端用。
MX记录，邮件交换记录，必须设置。
下面三个TXT记录虽说不是必须的，但非常重要。因服务器运行在公网，这三个记录可以增加服务器的“受信任”程度，特别是反垃圾箱。
拉到最下面点这里。

![](https://github.com/Eddlys/png/raw/main/cool/12.jpg)

分别点击创建这三条记录。

![](https://github.com/Eddlys/png/raw/main/cool/13.jpg)

![](https://github.com/Eddlys/png/raw/main/cool/14.jpg)

![](https://github.com/Eddlys/png/raw/main/cool/15.jpg)

![](https://github.com/Eddlys/png/raw/main/cool/16.jpg)


dns配置完成，等待生效。 

————————————————————— 


五，测试 
这里以QQ邮箱客户端为例，其他的都一样，电脑可使用foxmail，outlook等。
添加账号

![](https://github.com/Eddlys/png/raw/main/cool/17.jpg)

![账号密码](https://github.com/Eddlys/png/raw/main/cool/18.jpg)

![填写服务器配置,dns解析有](https://github.com/Eddlys/png/raw/main/cool/19.jpg)

往qq邮箱，gmail发送邮件测试。

![](https://github.com/Eddlys/png/raw/main/cool/20.jpg)

![](https://github.com/Eddlys/png/raw/main/cool/21.jpg)

往权威机构发送邮件，测试分数。

![](https://github.com/Eddlys/png/raw/main/cool/22.jpg)

![](https://github.com/Eddlys/png/raw/main/cool/23.jpg)

啊偶！spam直接扣了4.3分，这纯属滑稽行为，楼主明明启用了spam的。
没办法，只能把这个锅甩给freenom域名（楼主还真会找台阶下啊），但不影响使用。


————————————————————— 


六，总结 
教程虽然使用了免费域名，但也没有受到歧视。往qq，gmail发件并没有进入垃圾箱，可见，互联网世界公平正义还是有的。

转自酷安[奇点灰烬](https://www.coolapk.com/feed/32109178?shareKey=ZmU5MTJjMjBiM2FjNjI4ZGMyOGE~&shareUid=1139702&shareFrom=com.coolapk.market_11.4)



## 1

- [Pwn]()
- [notebook]()
- [taqini](//taqini.space)







