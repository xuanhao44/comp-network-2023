Lab7 常见问题（UDP）
================================================

.. toctree::
   :hidden:
   :maxdepth: 5

wireshark能捕获到UDP的收发包，但是UDP调试工具没有接收到？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
如下图所示：

.. image:: lab7-1.png

可能的解决方法：

wireshark有时候不校验IPv4首部校验和及UDP校验和，你需要在wireshark软件的菜单栏单击“编辑” —— “首选项”，在弹出的窗口中选择“Protocols”。

.. image:: lab7-2.png

找到“IPv4”，选择“Validate the IPv4 checksum if possible”。

.. image:: lab7-4.png

找到“UDP”，选择“Validate the UDP checksum if possible”。

.. image:: lab7-3.png


然后在wireshark的包分析窗口，分别检测IP头部和UDP头部的checksum字段是否正常。如果不正常，请自行检测IP的checksum16函数和UDP的udp_checksum函数。

.. image:: lab7-5.png

使用wireshark捕获的IP报文的checksum为什么是0？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

出现这种问题的原因，一般是开启电脑网卡的“硬件校验和”功能。只要开启该功能就不会进行校验和的计算，降低协议处理开销，从而提高数据转发的速度。然而，如果IP首部的IP地址遇到损坏，可能会对其他通信造成不好的影响。为了通过实验测试，还是建议同学们打开校验和检测功能。

.. image:: lab7-6.png

.. image:: lab7-7.png
   
UDP实验不能GDB调试吗？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
UDP实验也能用GDB调试，调试方法和Eth、ARP、IP、ICMP实验不一样，可参考 `main调试`_ 方法。

.. _main调试: ../appendix-b/1win.html#main

