.. 计算机网络实验（2022春季）| 哈工大（深圳） documentation master file, created by
   sphinx-quickstart on Tue Mar  1 11:08:07 2022.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

计算机网络实验指导书 - 2023春季 | 哈工大（深圳）
==================================================================

..  attention:: 
  本实验文档仅限于哈尔滨工业大学（深圳）《计算机网络》2023年课程实验使用，如有其他用途，请联系仇老师 qiujieting@hit.edu.cn。

.. important:: 
  请同学们遵守学术诚信，主动避免去阅读、使用任何人/互联网上的实验代码，也不要在互联网上公布你的代码，Coding的过程远比结果重要。
  
  所以，请你自己动手实践，日拱一卒，功不唐捐，静心享受Coding带来的快乐吧：）

.. note:: 
   我们已为自己制定了一个宏伟的目标：了解如何从最底层开始建造计算机网络。要实现这个目标，我们将从基本原理开始，然后提出再建造实际的网络时经常遇到的各种问题。在每一步，我们会利用现有的协议去说明各种可行的设计方案，但是并不将这些人为的协议视作不变的真理。相反，我们要不断地提出（并回答）网络为什么要如此设计的问题。在力求让人们理解现今网络的工作方式的同时认识基本概念是很重要的，因为随着技术的发展和新应用的不断出现，网络的变化日新月异。依照我们的经验，一旦人们理解了基本思想，对于遇到的任何新协议，消化和吸收起来都变得相对容易。

         ————来自《计算机网络系统方法》第一章

导读
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

本实验文档为哈尔滨工业大学（深圳）《计算机网络》课程实验指导材料。请大家先阅读 :doc:`/lab0/index`，对实验课程有一个整体的认识。关于配置实验，请先阅读 :doc:`/appendix-c/index`，掌握Cisco Packet Tracer的基本用法。关于协议栈编程实验，请先按照 :doc:`/appendix-b/index` 中的说明搭建实验环境，然后再从“协议栈之Eth协议实现”做起。对于有兴趣有余力的同学，建议完成 :doc:`/proplus/index`，若能完成会有意想不到的惊喜！

如果同学们对本课程实验内容、实验安排、实验指导书或者代码框架等各方面有宝贵的意见或建议，请私信老师或助教。


实验相关链接
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. 实验指导书仓库：https://gitee.com/hitsz-cslab/comp-network.git
#. 协议栈编程实验仓库：https://gitee.com/hitsz-cslab/net-lab.git
#. 邮件客户端实验仓库：https://gitee.com/hitsz-lab/maillab.git

.. _协议栈编程实验代码仓库:

获取协议栈编程实验框架代码
--------------------------

关于协议栈实验的实验环境，请参考 :doc:`/appendix-b/index` 

在命令中执行：

.. code-block:: sh
   :linenos:

   $ git clone https://gitee.com/hitsz-cslab/net-lab.git

获取框架代码，将克隆net-lab到当前目录。如遇问题请联系老师或助教。

.. _邮件客户端代码仓库:

获取邮件客户端编程实验框架代码
-----------------------------------
关于邮件客户端实验的实验环境，请参考 :doc:`/appendix-d/index` 

在命令中执行：

.. code-block:: sh
   :linenos:

   $ git clone https://gitee.com/hitsz-lab/maillab.git

获取框架代码，将克隆maillab到当前目录。如遇问题请联系老师或助教。

.. toctree::
   :maxdepth: 1
   :caption: 实验
   :hidden:

   lab0/index
   lab4/index
   lab2/index
   lab3/index
   lab8/index
   lab5/index
   lab6/index
   lab7/index
   lab10/index
   lab9/index   
   proplus/index
..
   lab0/index
   lab1/index
   lab2/index
   lab3/index
   lab4/index
   lab5/index
   lab6/index
   lab7/index
   lab8/index
   lab9/index
   lab10/index
   proplus/index

.. toctree::
   :maxdepth: 1
   :caption: 附录
   :hidden:

   appendix-a/index
   appendix-b/index
   appendix-c/index
   appendix-d/index
   appendix-e/index
   appendix-f/index
   appendix-g/index
   
