# BBR
漫谈BBR一键加速脚本

之前几年看了很多关于BBR一键加速脚本的讨论，关于BBR是什么，它的来源，在这里就不做介绍了

对于服务器的玩家，大部分都绕不开BBR怎么设置的话题，但是很多玩家都是专注于服务器的实际应用，不太想深入了解系统命令及系统搭建过程，于是网上就应运而生了许多BBR一键脚本了

抱着测试性能的目的我尝试了好些BBR一键加速脚本，当然测试性能必须安装一些网络监控软件，在监控性能的过程中顺带发现了一些有趣现象，基本上我尝试的BBR一键加速脚本都会不定时的在本机主动往外连接一些云端服务器，各国的数据中心都有，暴露出一键加速脚本有很多不可控因素，所以脚本还是自己写的靠谱 ( ´･ω･)ﾉ(._.`)  

小白看到这里会喷“我自己能写脚本要你在这废话” ＃－.－  

其实我也一直是小白，我总想学一些小白能懂的简单方法去解决大神们复杂脚本要解决的问题，因为我也不想深入了解系统命令及系统搭建过程 ＃－.－

于是我就去了解一下BBR的简单概念，发现BBR在Linux内核高于4.9的情况下是缺省支持的，只是要打开这个功能而已，那怎么去查Linux的内核呢？

只需要在一般用户或root用户打下面命令就可以了  
uname -a

输出大概是下面这个样子，不同linux版本稍有不同:   
Linux u20www 5.4.0-52-generic #57-Ubuntu SMP Thu Mar 6 6:57:66 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux

关键是5.4.0-52-generic这个代表看到的linux内核是5.4版本，比4.9要高，另外要注意的是内核版本有4.11这个状况，表示小版本是.11，还是比.9要高，不要看到.1就搞混了

那有些玩家会说我安装的时候怎么知道选那些版本的linux内核会满足要求呢?一般情况下ubuntu 18或以上，CentOS 8，Debian 9都满足内核版本要求，有些玩家一定要安装低版本或其他小众linux发行版怎么办？这个.........要升内核，只能闪，继续BBR一键加速脚本 ๐·°(৹˃̵﹏˂̵৹)°·๐  

确认好了内核版本，下面就可以几键打开BBR了

改用root用户并转到根目录   
$su  
#cd /  

改变sysctl.conf文件配置   
#echo "net.core.default_qdisc = fq" >> /etc/sysctl.conf   
#echo "net.ipv4.tcp_congestion_control = bbr" >> /etc/sysctl.conf  

使配置生效   
#sysctl -p

重启系统使BBR生效，各个linux版本命令稍有不同：   
ubuntu 20打入  
#poweroff --reboot  

ubuntu 18打入   
#shutdown -r now   

其他linux版本查一下相应命令

等系统重启完成后，改用root用户   
$su  

验证当前TCP控制算法的命令  
#sysctl net.ipv4.tcp_available_congestion_control  
返回值一般为  
net.ipv4.tcp_available_congestion_control = reno cubic bbr  
验证BBR是否已经启动   
#sysctl net.ipv4.tcp_congestion_control   
返回值一般为   
net.ipv4.tcp_congestion_control = bbr  
验证BBR是否已经启动   
#lsmod | grep bbr   
返回值有 tcp_bbr 模块即说明 bbr 已启动   

有些眼尖的玩家会发现，这个BBR显示IPv4已经启用了，那IPv6怎么弄？不用弄！因为linux的BBR只要IPv4启用了，IPv6也就启用了，因为没有IPv6单独启用BBR的配置命令。

还有一个要注意的地方是上面的输出结果全部都标明了TCP的字眼，就是“TCP”，没有“UDP”什么事 ๐·°(৹˃̵﹏˂̵৹)°·๐  
如果玩家的应用是服务器以UDP方式往外发数据，那么这个BBR可以洗洗睡了，BBR的整个原理是基于TCP的流量控制  
那非要UDP加速呢？只能用2倍3倍暴力UDP发包的程序，这个真不在漫谈范围

希望各位玩家撸得开心 ヾ(≧O≦)〃嗷~




