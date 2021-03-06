因为HTTP协议是无状态的，所以服务器需要记录用户的状态时，就需要某种机制来识别具体的用户，这个机制就是Session。

### Session

Session是在服务端保存的一种数据结构，保存的方法有很多，比如内存、数据库和文件等等。在大型的网站，一般会有专门的Session服务器集群，用来保存用户会话，这个时候Session信息都是放在内存的，使用一些缓存服务比如Memcached之类的来存放Session。

### Cookie

Cookie是在客户端（比如浏览器）中保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。服务端会在HTTP协议中告诉客户端，需要在Cookie中记录一个Session ID，以后每次请求把这个Session ID发送到服务器，服务器就能识别具体的客户。

如果客户端禁用Cookie怎么办？一般这种情况，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如sid=xxxx这样的参数，服务根据这个参数来识别用户。

Cookie的应用场景：假设你某次登录过一个网站，下次登录的时候不想再次输入账号了，怎么办？这个信息可以写到Cookie里面，访问网站的时候，网站页面的脚本可以读取Cookie里面的信息，就自动帮你把用户名和密码填了，能够方便用户。这也是Cookie名称的由来，给用户的一点甜头。

#### 以上内容摘自：

知乎：[https://www.zhihu.com/question/19786827](https://www.zhihu.com/question/19786827)

