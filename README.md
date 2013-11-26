本项目是一个关于GFW 的半研究性项目，尝试提供可用的翻墙方案，并找出关于GFW 的一些统计数据.

jjproxy
-------
由于完全不依赖代理服务器的方案已经无效，把西厢代理的代码都删了。
这个[jjproxy](https://github.com/liruqi/jjproxy) 神奇的地方在于用了标准的HTTP/HTTPS 和 SOCKS5代理服务器翻墙。目前可用。但用的是个人的VPS。如果有朋友愿意捐赠服务器，欢迎邮件告知。

数据统计
-------
做了一些有意思的不完全统计数据：[被封锁的IP列表](https://github.com/liruqi/west-chamber-season-3/blob/master/west-chamber-proxy/status/timedout-ip.list)(这个列表中存在大量的无HTTP服务的IP,目前尚未未做区分) 以及被[IP封锁的域名列表](https://github.com/liruqi/west-chamber-season-3/blob/master/west-chamber-proxy/status/timedout.txt)
另外，打算再维护一份被RESET的域名列表。

双向丢包
--------
服务器、客户端同时丢掉GFW 的干扰包。服务器上脚本 server.sh ，客户端脚本 client.sh. 如果路由器可以设置 iptables 防火墙(如Tomato 或 OpenWRT)，直接在路由器上设置即可：

    iptables -I FORWARD -p tcp -m tcp --tcp-flags RST RST -j DROP
    
目前这种方法还有问题。第一次丢包可以成功,但GFW把双方IP记入缓存; 第二次会被GFW发的SYN+ACK 干扰(一般而言这个干扰包会比服务器的先返回, 发现的特征是windows size 小于5000), 而且GFW会封锁被缓存住的正常的通信数据包,所以即使丢包也无法正常通信.
学术上, 这种攻击被定义为 Off-Path TCP Sequence Number Inference Attack [PDF](http://web.eecs.umich.edu/~zhiyunq/pub/oakland12_TCP_sequence_number_inference.pdf)
因此, 目前这种方式只能保证大概20秒内, 可以进行一次HTTP通信.

修改本地hosts文件
----------------
修改本地的 hosts 文件，并使用https 方式访问。参考[smarthosts](http://code.google.com/p/smarthosts/)项目。
 

反DNS污染
-------
修改hosts 文件部分解决了污染问题, 但是很可能不全. 要彻底解决DNS污染，设置国外的DNS 服务器，并本机丢弃GFW的 DNS伪包。

    a) 国外DNS服务器。大家比较熟悉的可能是[Google Public DNS](http://code.google.com/speed/public-dns/)。但是Google DNS经常出问题。先推荐两个，台湾中华电信的168.95.1.1 和 [OpenDNS](http://www.opendns.com/), 还不行，那就上午搜一个国外的DNS。

    b) 丢弃DNS伪包。
    * Windows: west-chmber的windows 移植目测不太好用，建议使用[DNSCrypt](http://www.opendns.com/technology/dnscrypt/)
    * Mac OS X: 建议使用[DNSCrypt](http://www.opendns.com/technology/dnscrypt/)。也可以尝试[kernet](https://github.com/liruqi/kernet/downloads)。实现TCP连接混淆，下最新的。运气好的话还能上blogspot。
    * Linux: 需要有iptables。如果 iptables 有 u32模块(或者你能自己搞定安装一个)，可以直接用本项目中的 client.sh；否则，只能自己编译原始的[西厢项目](http://code.google.com/p/scholarzhang)，具体操作看西厢的文档。

其它值得尝试的方法：[如何本地避免GFW的DNS污染](http://liruqi.info/post/28775426009/how-to-avoid-dns-hijack-locally)

TCP连接混淆
-----------
首先说明一下，[这东西很不靠谱](http://gfwrev.blogspot.com/2010/03/gfw.html)，容易受GFW 的更新而影响。感觉是，目前就kernet 项目效果还行。西厢的原始项目和Windows 移植现在都不好用。

其它工具
--------
[fqrouter](http://fqrouter.com/) 强大的开源翻墙工具，而且有详细的技术文档。测试过Android 版本，免配置，很好用。
[shadowsocks](http://www.shadowsocks.org/) 我用过的最省心的翻墙代理工具，依赖境外服务器使用。客户端和服务端都很稳定。
