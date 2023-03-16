排除网络故障
=====================

下面的组网图中，PC0无法ping通网关（192.168.2.1），PC1和PC2可以ping通它们自己的网关，请分析以下组网配置，找出PC0不能ping通网关的原因，并参照下列组网图，在Cisco Packet Tracer模拟器上构建出来拓扑图，分别在SW0、SW1、R0上写入相关配置命令，使得PC0、PC1和PC2都能ping通它们自己的网关，也就是都能上网。在完成本实验所有实验要求的情况下，实验分加0.5分（不会超过20分满分）。

实验要求
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. PC0属于VLAN2，PC1属于VLAN3，PC2属于VLAN4。
2. PC0、PC1和PC2都能ping通网关。

.. image:: cisco-vlan.png

**pvid** ：缺省的本地vlan（native VLAN），默认的pvid为1，也可以人为设置。进入接口模式，设置vlan-id（即vlan ID编号）作为本地vlan。配置方法参考下列步骤：

.. code-block:: sh
   :linenos:

    Switch(config)#interface f0/1
    Switch(config-if)#switchport trunk native vlan vlan-id

.. hint:: 
    如果Trunk两侧的PVID不匹配，会产生inconsistent local vlan，STP（Spanning Tree Protocol，生成树协议）会让端口处于阻塞状态，导致链路上不会转发。因此在本实验中，为了能让两侧PVID不匹配的trunk链路能转发数据，可以关闭STP协议。

    STP是将一个存在物理环路的交换网络变成一个没有环路的逻辑树形网络。根据IEEE802.1d协议（也叫做生成树协议）规定，接入网络的设备在与其他设备通信时，只有一条链路生效，其他冗余端口处于“阻断状态”。当且仅当这个链路除故障无法使用时，才会重新计算网络链路，让处于“阻断状态”的端口重新打开，这样即可保障了网络正常运转，又保证了冗余能力。

关闭STP的方法：

.. code-block:: sh
   :linenos:

    Switch(config)#no spanning-tree vlan vlan-id

.. note:: 
    路由器和交换机之间可以使用干道模式（Trunk模式），实现VLAN之间的通信。路由器可以从某一个VLAN接收数据包，并将这个数据包转发到另外一个VLAN。要实现VLAN间的路由，必须在一个路由器的物理接口上启用子接口，也就是将以太网口划分为多个VLAN间的连接，并配置成干道模式，这样每个VLAN对应一个子接口，也就能实现VLAN间路由了， 这种方式也称为 **单臂路由** 。

单臂路由的配置方法可参照下列步骤。

选定1841型号路由器。路由器在第一次配置时会启动配置向导界面，通常选择no，不进入对话模式，而是直接进入正常配置模式。

.. code-block:: sh
   :linenos:

     Router(config)#int f0/1    // 进入f0/1接口
     Router(config-if)#no shutdown  // 打开该接口     
     Router(config-if)#exit

     Router(config)#int f0/1.1  // 进入f0/1的子接口1
     Router(config-subif)#encapsulation dot1Q 2 // 指定子接口f0/1.1对应VLAN 2并封装成dot1Q协议
     Router(config-subif)#ip address 192.168.2.1 255.255.255.0 // 设置IP
     Router(config-subif)#exit

     Router(config)#int f0/1.2  // 进入f0/1的子接口2 
     Router(config-subif)#encapsulation dot1Q 3 // 指定子接口f0/1.2对应VLAN 3并封装成dot1Q协议
     Router(config-subif)#ip address 192.168.3.1 255.255.255.0
     Router(config-subif)#exit

     Router(config)#int f0/1.3  // 进入f0/1的子接口3
     Router(config-subif)#encapsulation dot1Q 4 // 指定子接口f0/1.3对应VLAN 4并封装成dot1Q协议
     Router(config-subif)#ip address 192.168.4.1 255.255.255.0
     Router(config-subif)#exit
     Router(config)#


常用的调试命令：

.. code-block:: sh
   :linenos:

    Switch#show vlan // 可以查看VLAN的信息，以及每个VLAN上有什么端口。
    Switch#show int trunk  // 可以查看trunk的端口信息。

实验提交
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

请提交实验报告。

实验报告需要画出组网图，给出设计方案、操作步骤、配置命令、实验结果及分析。