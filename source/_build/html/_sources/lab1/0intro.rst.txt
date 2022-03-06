实验目的
=====================

1. 熟悉Linux环境下基本的raw socket编程；
2. 分析以太网帧，通过修改程序完成不同层次PDU（Protocol Data Unit，协议数据单元）字段的分析。

实验任务
=====================
参考给定的抓包代码，编写你自己的抓包程序，使其能够分析ARP、ICMP协议或者更多的上层协议，并且能够模仿Wireshark的输出方式输出各字段信息。

实验原理
=====================

协议栈结构
--------------------------

计算机操作系统一般自带有网络协议栈、网卡驱动程序和网卡（网络硬件设备），它们相互配合来实现网络数据信息的收发。

.. image:: tcpip.png


上图是TCP/IP网络协议栈的内部结构图。图中分成应用程序、操作系统TCP/IP协议栈、驱动程序、硬件四个部分，它们分别承担不同的功能。最上层是由用户调用的应用程序，比如浏览器、邮件客户端、web服务器、邮件服务器等程序，这些应用程序会调用Socket库来完成网络数据的收发工作。尽管不同的应用程序收发的数据内容不同，但收发数据的操作是相通的。

接下来，Socket库会委托操作系统内部的协议栈来完成。TCP/IP协议栈是分层结构，协议栈上面是TCP或UDP协议栈，中间是IP协议，下面是以太网协议栈。不同的应用程序可根据需求来决定使用TCP或是UDP协议。例如，浏览器、邮件等一般的应用程序收发数据时用TCP，DNS查询等收发较短的控制数据时用UDP。中间层IP协议用于控制网络包收发操作。此外，IP还包括ICMP协议和ARP协议。ICMP协议用于告知网络包传送过程中产生的错误以及各种控制消息，ARP用于根据IP地址查询相应的以太网MAC地址。下面的以太网协议用于添加或去除MAC地址等信息，将其封装或解封数据链路层的数据帧。协议栈下层是网卡驱动程序，负责控制网卡硬件，而最下面的网卡则负责完成实际的收发操作，也就是对网线中的信号执行发送和接收的操作。

socket套接字
--------------------------

应用程序调用socket申请创建套接字，协议栈根据应用程序的申请执行创建套接字的操作。

套接字的实体就是协议栈分配的用于存放一个套接字所需的的内存空间，在这块内存空间里主要存放 **通信控制信息** ，例如通信对象的IP地址、端口号、通信操作的状态等。套接字刚刚创建时，首先分配一个套接字所需的内存空间，但数据收发操作还没有开始，只向其中写入表示初始状态的控制信息。

.. code-block:: c
   :emphasize-lines: 3
   :linenos:

   #include <sys/socket.h>

   int socket(int family,int type,int protocol);
   // 返回：非负描述字 —— 成功，-1 —— 出错

其中family参数指明协议栈（family），它是表 :ref:`socket函数的协议族family常值` 的某个常值。该参数也往往被称为协议域（domain）。type参数指明套接口类型，它是表 :ref:`socket函数的套接口（type）常值` 的某个常值。protocol参数应设计为表 :ref:`socket函数AF_INET或AF_INET6的协议类别（protocol）常值` 的某个协议类型常值，或者设为0，以选择所给定family和type组合的系统缺省值。

.. _socket函数的协议族（family）常值:

.. list-table:: socket函数的协议族（family）常值
   :widths: 20 30
   :header-rows: 1
   :align: center

   * - family
     - 说明
   * - AF_INET
     - IPv4协议
   * - AF_INET6
     - IPv6协议
   * - AF_LOCAL
     - Unix域协议
   * - AF_ROUTER
     - 路由套接口
   * - AF_KEY
     - 密钥套接口
  
.. _socket函数的套接口（type）常值:

.. list-table:: socket函数的套接口（type）常值
   :widths: 20 30
   :header-rows: 1
   :align: center

   * - type
     - 说明
   * - SOCK_STREAM
     - 字节流套接口
   * - SOCK_DGRAM
     - 数据报套接口
   * - SOCK_SEQPACKET
     - 有序分组套接口
   * - SOCK_RAW
     - 原始套接口

.. _socket函数AF_INET或AF_INET6的协议类别（protocol）常值:

.. list-table:: socket函数AF_INET或AF_INET6的协议类别（protocol）常值
   :widths: 20 30
   :header-rows: 1
   :align: center

   * - protocol
     - 说明
   * - IPPROTO_TCP
     - TCP传输协议
   * - IPPROTO_UDP
     - UDP传输协议
   * - IPPROTO_SCTP
     - SCTP传输协议

一般来说，socket的type值主要是SOCK_STREAM和SOCK_DGRAM，但是它们并不能很好地满足对于数据链路层整体包的捕获要求。Raw socket套接字可用于在MAC层上收发原始数据帧，这样就允许用户在用户空间完成MAC之上各个层次的收发，进而可以对整个网络的数据进行监控和控制，给开发或测试带来了极大的便利性。因此，在本次实验中，我们需要使用原始套接字SOCK_RAW来获取MAC层上的原始数据帧，可以参考以下方式创建能够发送和接收 **以太网数据帧** 的Raw socket套接字：
   
.. code-block:: c
   :linenos:

   #include <sys/socket.h>

   socket_fd = socket(PF_PACKET, SOCK_RAW, htons(ETH_P_IP |ETH_P_ARP |ETH_P_ALL));

.. important:: 
  当创建SOCK_RAW套接字时，需要有超级用户特权，这样可以防止恶意应用程序绕过内建安全机制来创建报文。

在Linux中，调用man可以查看相关命令的调用细节， 如 man socket。

.. image:: man-socket.jpg



创建完一个socket之后，就可以调用recvfrom()函数接收数据链路层的数据帧了。

.. code-block:: c
   :linenos:

   recvfrom(int socket_fd,void* buf,size_t len,int flags,struct sockaddr * src_addr,socklen_t *addrlen);

其中buf是收到的包所存放的位置。

以太网帧数据帧格式
--------------------------

当recvfrom()函数接收到数据链路层的数据帧后，我们需要进一步分析数据帧格式。

.. image:: 以太网数据帧.png

上图是在网线上传输的数据包。其中报头/起始帧分界符、FCS（Frame Check Sequence，帧校验序列）是由网卡设备负责的部分。我们只需要关注MAC头部、IP头部、TCP/UDP头部、数据即可。

数据链路层的数据帧格式如下图所示。

.. image:: 帧格式.png
  :scale: 60 %

*  **MAC目的地址** ：接收帧的网络适配器的物理地址（MAC地址），6个字节。当网卡接收到一个数据帧时，首先会检查该帧的目的地址，是否与当前适配器的物理地址相同，如果相同，就会进一步处理；如果不同，则直接丢弃。
*  **MAC源地址** ：发送帧的网络适配器的物理地址，6个字节。
*  **长度/类型** ：当该值在0x05DC（10进制数为1500）以下时，表示该以太网数据帧的长度；在0x0600以上时，则表示上层协议的类型，2个字节，标识数据交付哪个协议处理。例如，字段为0x0800时，表示将数据交付给IP协议。字段为0806时，表示该数据帧时ARP请求/应答报文。

更多的以太网数据帧、IP协议报文格式和ARP报文格式的详细资料请参考Internet工程任务组（IETF）提供的官方标准文档 `RFC0894 <https://www.ietf.org/rfc/rfc0894.txt>`_ 、 `RFC0791 <https://www.ietf.org/rfc/rfc0791.txt>`_ 和 `RFC0826 <https://www.ietf.org/rfc/rfc0826.txt>`_ 。

实验实现
=====================

下面给出一个简单的抓包代码。该程序不断地接收数据链路层的数据包，并显示包头类型和部分内容。

.. code-block:: c
   :linenos:

    #include <stdio.h>
    #include <unistd.h>
    #include <sys/socket.h>
    #include <sys/types.h>
    #include <linux/if_ether.h>
    #include <netinet/in.h>

    #define BUFFER_MAX 2048

    int main(int argc,char* argv[]){
      int fd;
      int proto;
      int str_len;
      char buffer[BUFFER_MAX];
      char *ethernet_head;
      char *ip_head;
      unsigned char *p;
      
      if((fd=socket(PF_PACKET,SOCK_RAW,htons(ETH_P_ALL)))<0){ 
        printf("error create raw socket\n");
        return -1;
      }
      
      while(1){
        str_len = recvfrom(fd,buffer,2048,0,NULL,NULL);

        // 请思考，此处为什么要丢弃小于42字节的包？
        if(str_len < 42) 
        {
          printf("error when recv msg \n");
          return -1;
        }
        
        ethernet_head = buffer;
        p = ethernet_head;
        printf("Src MAC address: %.2x:%02x:%02x:%02x:%02x:%02x\n",
          p[6],p[7],p[8],p[9],p[10],p[11]);

        printf("Dst MAC address: %.2x:%02x:%02x:%02x:%02x:%02x\n",
          p[0],p[1],p[2],p[3],p[4],p[5]);	
        ip_head = ethernet_head+14;
        p = ip_head+12;
        printf("Src IP: %d.%d.%d.%d\n",p[0],p[1],p[2],p[3]);
        printf("Dst IP: %d.%d.%d.%d\n",p[4],p[5],p[6],p[7]);	
        proto = (ip_head + 9)[0];
        p = ip_head +12;
        printf("Protocol:");
        switch(proto){
          case IPPROTO_ICMP:printf("icmp\n");break;
          case IPPROTO_TCP:printf("tcp\n");break;
          case IPPROTO_UDP:printf("udp\n");break;
          default:
          break;
        }
      }
      return -1;
    }

**Step1** ：在Linux系统上编译该程序：

.. code-block:: console
   :linenos:

    $ gcc socket_dump.c ‒o socket_dump

**Step2** ：编译成功之后运行 (运行时需要 root 权限)

.. code-block:: console
   :linenos:

    $ sudo ./socket_dump

**Step3** ：在linux系统命令行上 ping www.baidu.com，socket_dump.c 程序输出如下：

.. figure:: socketdump.jpg

**Step4** ：参考附录 :doc:`Wireshark 入门 <../appendix-a/index>` 学习使用Wireshark，分析Wireshark的输出格式。

**Step5** ：参考上述代码，编写你自己的抓包程序，使其能够分析ARP、ICMP协议或者更多的上层协议，并且能够模仿Wireshark的输出方式输出各字段信息。



实验提交
=====================

提交完整的程序以及实验设计报告。

代码及报告提交方式

--------------------------
.. hint:: 
  请大家注意提交截止时间，不要错过了！

Step1：登录HITsz Grader，用户名和密码都是学号（若学号中有字母，则为大写）。

Step2：进入课程

.. image:: 提交1.png
  :scale: 50%

Step3：进入作业作答页面

.. image:: 提交2.png
  :scale: 50%

Step4：点击选择文件

.. image:: 提交3.png
  :scale: 50%

.. image:: 提交4.png
  :scale: 50%

Step5：点击提交

.. image:: 提交5.png
  :scale: 50%