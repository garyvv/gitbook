# tcp 大量 wait 解决

> 在PHP 项目做一次  curl 的 proxy时，没设置超时时间，第三方也没设置超时，导致TCP 链接一直 wait，链接数被占用消耗

- 消耗完链接数后，curl 提示 cURL error 56: Recv failure: Connection reset by peer (see http://curl.haxx.se/libcurl/c/libcurl-errors.html)


### 解决

- netstat命令的功能是显示网络连接、路由表和网络接口的信息，可以让用户得知有哪些网络连接正在运作。在日常工作中，我们最常用的也就两个参数，即netstat –an

- netstat  -an参数中stat（状态）的含义如下：
  - LISTEN：侦听来自远方的TCP端口的连接请求；
  - SYN-SENT：在发送连接请求后等待匹配的连接请求；
  - SYN-RECEIVED：在收到和发送一个连接请求后等待对方对连接请求的确认；
  - ESTABLISHED：代表一个打开的连接，我们常用此作为并发连接数；
  - FIN-WAIT-1：等待远程TCP连接中断请求，或先前的连接中断请求的确认；
  - FIN-WAIT-2：从远程TCP等待连接中断请求；
  - CLOSE-WAIT：等待从本地用户发来的连接中断请求；
  - CLOSING：等待远程TCP对连接中断的确认；
  - LAST-ACK：等待原来发向远程TCP的连接中断的确认；
  - TIME-WAIT：等待足够的时间以确保远程TCP连接收到中断请求的确认；
  - CLOSED：没有任何连接状态；

- CentOS_消除未被及时释放的TIME_WAIT状态的TCP连接

> 如发现系统存在大量TIME_WAIT状态的连接，通过调整内核参数解决，

vim /etc/sysctl.conf
编辑文件，加入以下内容：
```
  net.ipv4.tcp_syncookies = 1
  net.ipv4.tcp_tw_reuse = 1
  net.ipv4.tcp_tw_recycle = 1
  net.ipv4.tcp_fin_timeout = 30
```
然后执行 /sbin/sysctl -p 让参数生效。

net.ipv4.tcp_syncookies = 1 表示开启SYN cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
net.ipv4.tcp_tw_recycle = 1 表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭。
net.ipv4.tcp_fin_timeout 修改系統默认的 TIMEOUT 时间
