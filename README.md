# secretweapon 秘密武器
在Java开发之路上的知识集合。
Knowledges collection involving any aspects of Java development.
## 网络基础
### TCP/IP协议

OSI七层参考模型与TCP/IP体系结构，以及参照前两者优点的五层协议。
OSI体系结构
TCP/IP体系结构
五层协议结构
7. 应用层
应用层
（各种应用层协议如：TELNET，FTP，SMTP等）
5. 应用层
6. 表示层
5. 会话层
4. 传输控制层
传输控制层（TCP或UDP）
4. 传输控制层
3. 网络层
网际层IP
3. 网络层
2. 数据链路层
网络接口层
2. 数据链路层
1. 物理层
1. 物理层
基于TCP/IP协议，只需要关心运输层以上的部分即可。因为计算机内核程序（kenel）中已经默认实现了运输层，网络层，数据链路层以及物理层。
三次握手
TCP——面向连接，可靠的传输协议
• 连接：
1. 客户端传输控制层产生一个sync信号的数据包，发送给服务端。
2. 服务端收到客户端发送的sync信号的数据包，返回一个sync+ack的数据包给客户端。
3. 客户端收到服务端发送的sync + ack的数据包，返回一个ack信息的数据包给服务端。
至此三次握手已经完成，只有三次握手完成之后，双方才能为本次会话开辟资源（创建出线程或者fork出进程）。双方把用于本次会话的资源支起，能够响应后续对方发送的数据。
以上整个过程称为连接。
• 可靠：
不是使用两次握手，而是采用三次握手；三次握手确保了建立连接的时刻客户端与服务端双向的链路通畅以及收发能力。
四次挥手
在计算机内核当中，将传输控制层及以下的部分，是按照套接字（Socket）的方式处理的。
单个Socket套接字信息
发送方 ip + port
接收方 ip + port
在日常开发过程中一般不会太关注发送方接收方的ip+port信息。其实在内核中已经帮我们对应好了发送与接收方的信息。
运行的程序都需要指定一个端口号，与本机的端口号（一台计算机最多可以拥有2^16-1=65535个端口号）建立起map映射关系，才能通过网络进行数据的收发。
在操作系统中有大量的数据流通，内核就是根据信息记录的端口号来决定从本机什么应用当中取信息，发送到何处，以及接收到的信息从何处来，到本机哪个应用中去的。因此在开发过程中遇到过端口号被占用的情况，其原因就是端口号必须是某一个程序的唯一表示。
当同一个客户端与同一台服务器建立多个连接，也是因为有了套接字的两方ip + port信息的存在，所以多个连接之间互不干扰，都是互相独立的。
当一台计算机与同一台服务器建立两个ssh连接时，服务端使用命令
# netstat -natp
参数：n表示不用类似localhost类的字符代替IP，即显示完整IP；a代表所有；t代表tcp连接；p代表显示进程PID。
查看到的信息：
 一台计算机的端口号最多为65535个，因此也是数据有限的资源。在使用完毕之后，应该关闭端口。而方式，则是四次挥手。
为什么关闭是四次？
TCP是面向连接的，连接双方都应该确认关闭，而后各自释放对应资源。
挥手过程（挥手的起点可以为客户端或服务端的任意一方）：
1. 挥手（结束）发起方发送一个fin数据包给接收方；
2. 接收方收到fin数据包之后，立刻发送一个ack数据包告诉发起方自己已经接收到了断开连接请求；
3. 接收方在完成自己的本次会话的信息传输之后，进入可以断开连接的状态，然后向断开连接的发起方发送一个fin数据包表示可以断开请求了。
4. 发起方在收到接收方发出的fin数据包之后，也立即发送一个ack数据包告诉接收方，自己也接收到了对方的结束连接请求。
而后双方按照自己的资源释放策略，在合适的时候各自释放本次连接所需要的资源。

## 数据结构&算法

## 设计模式

## Java基础

## JVM

## 数据库

## Spring
