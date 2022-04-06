
Windows开发环境搭建
==================================================


实验工具
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

VSCode
------------------------------ 
Visual Studio Code 是一款功能强大的代码编辑器, 适用于几乎任何语言并可在任何操作系统上运行。通过 VSCode 的强大扩展库，我们可以在 VSCode 上一站式创建，编辑，构建，运行和调试远程主机上的工程文件，就像使用 Code::Blocks 操作本地工程一样。

在https://code.visualstudio.com/ 下载并在物理机上安装 VSCode 软件。

npcap
------------------------------ 
npcap是一个网络数据包抓包工具，它是WinPcap的改进版，支持WIndows平台的回环数据包采集和发送。

npcap下载地址：
https://npcap.com/#download

.. note:: 
   npcap版本必须大于1.0

cmake
------------------------------ 
CMake是一个跨平台的编译工具。它可以跨平台生成对应平台能使用的Makefile，这样我们就不用再自己去修改Makefile了，非常地方便。那它又是根据什么生成的makefile呢？就是一个叫CMakeLists.txt文件去生成Makefile，当然，这个CMakefile.txt是要我们自己编写的。本实验已经提供好了CMakefile.txt文件，可大家学习参考：）

CMake自带有ctest工具可以用于代码测试。再使用CMakefile.txt文件编译工程的时候，CTest会自动configure、build、test和展示测试结果。

CMake的下载地址：
https://cmake.org/download/

Windows 64位系统建议选择下面标红框框的版本：

.. image:: cmake.png

下载完成后，双击进行安装。在安装过程建议选择“Add CMake to the system PATH for all users”

.. image:: cmake1.png
   :scale: 70%

gcc
------------------------------ 
推荐大家安装tdm-gcc，tdm-gcc是一个Windows编译器套件，包括gcc、g++等，工具比较齐全。

tdm64-gcc下载地址：
https://github.com/jmeubank/tdm-gcc/releases/download/v10.3.0-tdm64-2/tdm64-gcc-10.3.0-2.exe

Windows下编译和调试
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
打开VSCode软件，在左侧边栏的扩展商店搜索并安装以下扩展：

1. Chinese (Simplified) Language Pack for Visual Studio Code
#. C/C++
#. CMake Tools

安装完成后重启VSCode。

点击打开文件夹打开工程目录后即可进行代码操作（ **确保CMakeLists.txt处于当前根目录中** ）。

.. image:: vscode2.png
   :scale: 50 %


首次配置会弹出选择编译工具的提示，选择带有GCC的选项。需要在安装tdm-gcc后才能找到GCC的选项，如果安装tdm-gcc后没有显示该选项，点击[Scan for kits] Search for compilers on this computer，搜索gcc（如果还是没有找到，建议重启电脑）。

.. image:: vscode3.png

在配置好VSCode环境之后，即可使用CMake工具栏完成编译和调试操作。如果想要对main进行编译和调试，可以在main[main.exe]这一项点击右键，再点击“生成”进行编译。

.. image:: vscode1.png
   :scale: 50 %

编译完成后，可以在代码行前增加断点，然后选择“调试”，接下来，就可以愉快地进行暂停、单步跳过、单步调试、单步跳出、重启、停止等这些调试操作了。

.. image:: vscode4.png

.. hint:: 
   协议栈的Eth、ARP、IP、ICMP协议实验提供了自测环境，该自测环境是自构建了一套读写离线数据包的驱动层，然后通过对比log和pcap文件来分析这些协议是否能收发。因此， **在Eth、ARP、IP、ICMP协议实验中，不要修改config.h头文件中的NET_IF_IP和NET_IF_MAC** 。

   但当我们做到UDP实验时，需要用到网络上真实的UDP调试工具与我们自构建的协议栈进行点对点通信，以此来测试整个网络协议栈是否能正常收发。我们的协议栈通过虚构了一张虚拟网卡，由虚拟网卡和真实网卡进行通信，这两张网卡的IP地址必须要不一样，但是要保证它们处于同一个网段内。因此， **在UDP实验调试之前，必须修改include/config.h头文件中的NET_IF_IP宏定义** ，即需要自定义网卡的IP地址。该IP地址的网络号必须与你自己电脑中真实网卡的网络号一致（也就是和真实网卡处于同一个网段内）。注意：此处是 **网络号** 要和真实网卡一致，以确保它们处在同一个网段内， **不是将自定义网卡（虚拟网卡）的IP地址设置成真实网卡的IP地址** 。

   .. image:: vscode5.png   
