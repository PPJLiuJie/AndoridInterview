ping可以说是ICMP协议的最著名的应用，是TCP/IP协议的一部分。

利用ping命令可以检查网络是否联通，可以很好地帮助我们分析和判定网络故障。

网络检测时，排错的顺序与方法：

1. ping 127.0.0.1 检查TCP/IP协议栈是否正常
2. ping 本地ip （比如：192.168.0.43） 检查网卡是否正常工作
3. ping 网关地址（比如：192.168.0.1）检查和网关的连接是否正常
4. ping 远程网站 （比如：www.baidu.com）检查远程连接性

**另外，如果你需要使用固定IP，比如我个人喜欢用“192.168.1.222”这个IP，那么你需要使用“ping 192.168.1.222”来确定这个IP是否已经被占用，如果ping通了，说明已经被占用。**

### ping的整个过程：

![](/assets/0007.png)

这里讲两种情况，一种是同一网段内的ping，另一种是跨网段的ping

#### 1 同网段内ping

如果主机A要去ping主机B，命令为“ping 1.1.1.3”。

1. ping命令会构建一个固定格式的ICMP请求数据包，然后由ICMP协议将这个数据包连同地址“1.1.1.3”一起交给IP层，IP层将以“1.1.1.3”做为目标地址，以本机IP做为源地址，加上一些其他的控制信息，构建一个IP数据包，以便交给数据链路层构建一个数据帧。
2. IP层协议通过主机B的IP地址和自己的子网掩码，发现主机B和主机A属于同一子网络，就直接在当前子网络内查找主机B的MAC地址，如果之前两台主机之间有过通信，那么主机B的ARP缓存中缓存了主机B的IP地址与其MAC地址的映射。如果没有缓存，就发送一个ARP请求广播，得到主机B的MAC地址。
3. 通过ARP协议获取到的主机B的MAC地址，将被交给数据链路层，数据链路层构建一个数据帧，目标地址是主机B的MAC地址，源地址是主机A的MAC地址，再加上一些其他控制信息，依据以太网的介质访问规则，将它们发送出去。
4. 主机B收到这个数据帧后，先检查数据帧的目标MAC地址是否和自己的MAC相同，如相同，则接收该数据帧，将IP数据包从数据帧中提取出来，交给本机的IP层处理；否则丢弃数据帧。
5. 主机B的IP层收到IP数据包后，将有用的信息提取后交给ICMP协议，后者处理后，马上构建一个ICMP应答包，发送给主机A，其过程和主机A发送ICMP请求包到主机B一模一样。

#### 2 跨网段的ping

如果主机A要去ping主机C，命令为“ping 2.1.1.1”。

1. 前面的准备工作跟同网段内ping一样，到了怎样获取主机C的MAC地址时，IP层协议通过计算发现主机C与主机A不在同一子网络，就直接交给路由器处理，也就是将路由器端口1的MAC地址当做目标的MAC地址。至于怎样得到路由的MAC地址，使用的依然是ARP协议，先在ARP缓存中查找路由器的MAC地址，找不到就广播。
2. 路由器端口1得到数据帧之后，将数据帧发送到端口2，端口2需要将数据帧发给主机C，那么端口2需要知道主机C的MAC地址，跟上面一样，使用ARP协议，从ARP缓存中查找，找不到就广播。
3. 后续步骤跟同网段内ping的步骤类似。。。

### ping的返回结果分析：

![](/assets/008.png)

以ping百度为例。

www.baidu.com是www.a.shifen.com的DNS别名，IP地址是14.215.177.38

TTL：time to live 。每一个被发送出的IP数据包都有一个TTL域，该域被设置为一个较高的数值（在本例中ping信息包的TTL值为56\)。当信息包在网络中被传输时，TTL的域值通过一个路由器时递减1；当TTL 递减到0时，信息包被路由器抛弃。

### 路由器知识点补充：

1. 路由器都有独立且不重复的MAC地址。
2. 商用路由器每个端口都有MAC地址。家用路由器不同与商用，家用只有两个，一个用于WAN口，一个用LAN口。下图所示的就是家用路由器WAN口和LAN口的MAC地址。
3. 路由器是一个硬件，网关是一个概念，只要连接两个不同网络的设备都可以叫网关，网关可以设置在路由器上，一般一个局域网会设置一个网关，供一个网段使用。

![](/assets/0005.png)

![](/assets/0006.png)

以上内容参考自：

[https://baike.baidu.com/item/PING/6235\#11](https://baike.baidu.com/item/PING/6235#11)

[https://zhidao.baidu.com/question/1796579256282821107.html](https://zhidao.baidu.com/question/1796579256282821107.html)

[http://blog.51cto.com/wanicy/335207](http://blog.51cto.com/wanicy/335207)

[https://zhidao.baidu.com/question/409920562.html](https://zhidao.baidu.com/question/409920562.html)

[https://www.zhihu.com/question/21787311?from=profile\_question\_card](https://www.zhihu.com/question/21787311?from=profile_question_card)

[https://blog.csdn.net/u010240427/article/details/52585841](https://blog.csdn.net/u010240427/article/details/52585841)

