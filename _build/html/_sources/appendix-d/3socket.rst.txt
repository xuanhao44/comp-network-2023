socket编程简介
================================================

.. toctree::
   :hidden:
   :maxdepth: 5

关于socket的扩展阅读：https://mp.weixin.qq.com/s/Syee1T7JcnqACs5TJf-cLA

socket概述
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
socket（套接字）是一种独立于协议的网络编程接口。socket接口定义了许多函数或例程，我们可以使用它们来开发TCP/IP网络上的应用程序。

.. _socket的类型:

socket的三种类型：

1. 流式socket（SOCK_STREAM）：使用TCP协议提供可靠的、面向连接的通信流，保证了数据传输的正确性和顺序。
2. 数据报socket（SOCK_DGRAM）：使用UDP协议提供无连接的服务，数据通过相互独立的报文进行传输，是无序的，并且不保证可靠、无差错。
3. 原始socket（SOCK_RAW）：允许在MAC层上收发原始数据帧，功能强大但使用较为不便，主要用于一些协议开发。

在邮件客户端实验中使用的SMTP和POP3协议，都是运行在TCP协议的基础上，因此，我们下面主要介绍流式socket（SOCK_STREAM）的用法，如果想了解socket的另外两种类型的用法，可以自行百度。

流式socket（SOCK_STREAM）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
网络应用的标准模型是客户端/服务器模型（Client/Server，简称C/S）。客户端一般是发起通信请求的进程，服务器则一般是等待并处理客户请求的进程。服务器通常由系统执行，在系统生成期间一直存在。流式socket中服务器和客户端的交互过程可参考下图。

.. image:: socket-1.png

服务器的处理过程如下：

1. 调用scoket初始化套接字；
2. 调用bind将套接字与服务器的IP绑定在一起，客户端可以使用这个IP地址进行连接；
3. 调用listen开始侦听客户端建立连接的请求；
4. 调用accept处理收到的客户连接请求；
5. 调用recv和send接收和发送数据，与客户端通信；
6. 最后调用close关闭并释放socket。注意，当服务器关闭socket后，客户端将无法连接。

客户端的处理过程如下：

1. 调用socket初始化套接字；
2. 调用connect连接指定的服务器（IP地址）和端口；
3. 连接建立后，调用recv和send与服务器通信；
4. 最后调用close关闭并释放socket。

在邮件客户端实验中，只需完成客户端的处理，故以下只介绍客户端使用到的socket套接字编程接口。
   
socket函数
----------------------------------
socket函数用于创建并初始化一个socket套接字接口。

.. code-block:: c
   :linenos:

   //使用socket编程接口所需的头文件
   #include <sys/socket.h>
   #include <netinet/in.h>

   int socket(int domain, int type, int protocol);
   // 返回：非负描述字 —— 成功，-1 —— 出错

1. domain参数指明协议族，在使用IPv4协议时，该参数为AF_INET；
2. type参数指明 socket的类型_ ，在邮件客户端实验中该参数为SOCK_STREAM；
3. protocol参数指名协议类型，建议设为0，以选择所给定family和type组合的系统缺省值。
   
connect函数
----------------------------------
connect函数用于向指定的服务器和端口发送连接请求。

.. code-block:: c
   :linenos:

   int connect(int sockfd, struct sockaddr * servaddr, int addrlen);
   // 返回：0 —— 成功，-1 —— 出错

1） sockfd参数是socket函数返回的套接字描述符；

2） servaddr参数是服务器的地址和端口，它由struct sockaddr结构体来表示，其定义如下：

.. code-block:: c
   :linenos:

   struct sockaddr_in{
      short in sin_family; //地址族
      unsigned short int sin_port;  //端口号
      struct in_addr sin_addr;   //IP地址
      unsigned char sin_zero[8]; //填充
   }

1. sin_family表示协议的地址族，IP协议必须为AF_INET；
2. sin_port表示TCP/UDP的端口号，可别忘记大小端转换了：）
3. sin_zero用于使各种协议族保持结构大小一致的填充字段，IP协议中，此字段填充为8位的零（建议使用bzero函数）；
4. sin_addr表示IP地址，它也是一个结构体。
   
.. code-block:: c
   :linenos:

   struct in_addr{
      unsigned long s_addr; 
   }

与常见的IP地址（如“192.168.1.0”）不同，struct in_addr结构体定义的IP地址是一个无符号长整形的数，所以在填充地址时，需要将字符串表示的IP地址与32位二进制形式的IP地址进行转换。在邮件客户端代码框架中，我们调用gethostbyname函数根据DNS域名获取到了char类型的dest_ip，接着可以使用inet_addr函数把它转换成32位二进制网络字节序的IPv4地址。

.. code-block:: c
   :linenos:

    #include <arpa/inet.h>
    
    unsigned long inet_addr(const char* strptr);

3） addrlen参数是套接字地址结构（sockaddr）的长度。

send函数
----------------------------------
send函数实现网络数据的发送。

.. code-block:: c
   :linenos:

   int send(int sockfd, void * buf, int len, int flags);
   // 返回：表示实际发送了多少数据，-1 —— 出错

1. buf参数是发送数据的缓冲区指针；
2. len参数是缓冲区的大小；
3. flags参数一般取0。

recv函数
----------------------------------
recv函数实现网络数据的接收。

.. code-block:: c
   :linenos:

   int recv(int sockfd, void * buf, int len, int flags);
   // 返回：表示实际接收了多少数据，-1 —— 出错

1. buf参数是接收数据的缓冲区指针；
2. len参数是缓冲区的大小；
3. flags参数一般取0，在接收缓冲区有数据时立即返回。
   
.. note:: 
   recv函数和send函数是阻塞的。当调用recv函数时，程序会一直阻塞知道接收到数据（或者发生了中断或者错误）才会返回。
