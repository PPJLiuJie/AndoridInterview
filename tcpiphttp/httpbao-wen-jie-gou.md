## HTTP的报文结构

#### 1 HTTP请求报文

* User-Agent：发送请求的浏览器类型
* Accept ：客户端可识别的响应内容类型列表
* Accept-Language：客户端可接受的自然语言
* Accept-Encoding：客户端可接受的编码压缩格式
* Accept-Charset：客户端可接受的应答字符集
* Host：请求资源所在的主机和端口号，允许多个域名共用一个IP地址，即虚拟主机
* Connection：连接方式（close或keey-alive）
* Cookie：存储于客户端的扩展字段，向同一域名的服务器发送属于该域的cookie
* 请求包体：在POST方法中使用
* Referer：包含一个URL，用户从该URL代表的页面出发访问当前请求的页面
* If-Modified-Since：文档的最后改动时间

#### 2 HTTP响应报文

* Allow：服务器支持哪些请求方式（如POST、GET）
* Content-Encoding：文档的编码



