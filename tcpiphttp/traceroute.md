Traceroute是用来侦测主机到目的主机之间所经路由情况的重要工具，也是最便利的工具。

Traceroute的原理非常非常有意思。它收到到目的主机的IP后，首先给目的主机发送一个TTL=1（Time To Live）的UDP数据包，而经过的第一个路由器收到这个数据包以后，就自动把TTL减1，而TTL变为0以后，路由器就把这个包给抛弃了，并同时产生一个主机不可达的ICMP数据报给主机。主机收到这个数据报以后再发一个TTL=2的UDP数据报给目的主机，然后刺激第二个路由器给主机发ICMP数据报。如此往复直到到达目的主机。这样，traceroute就拿到了所有的路由器IP。

![](/assets/0010.png)

#### 以上内容摘自：

[https://github.com/LRH1993/android\_interview/blob/master/computer-networks/tcpip.md](https://github.com/LRH1993/android_interview/blob/master/computer-networks/tcpip.md)

