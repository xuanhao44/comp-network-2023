Linux开发环境搭建
==================================================


为了简化环境，避免其他无关细节的干扰，如路由器、交换机等等，Linux网络编程实验环境是在一台物理计算机和Ubuntu虚拟机环境（也可以采用其他的Linux虚拟机环境）下进行。

网络实验室提供有已经安装好的Ubuntu虚拟机环境，如果你想在自己的电脑上进行编译调试，那么你需要自行安装Linux虚拟机环境，虚拟机网络适配器选择为 NAT 模式。

.. image:: 虚拟机.png
   
那么你在安装完Ubuntu虚拟机之后，还需要安装所有可能需要的依赖：

.. code-block:: sh
   :emphasize-lines: 1-3
   :linenos:

    sudo apt-get install git make cmake libpcap-dev openssh-server
    sudo apt-get install libreadline-dev libncurses-dev wireshark tshark g++


.. image:: 实验环境.png



上图是给出的示例图。其示例图中，物理机和虚拟机的网络号192.168.3.0，子网掩码255.255.255.0，虚拟机的网卡IP地址192.168.163.134，确保它们处在同一个网段内。你可以根据你电脑中虚拟网卡的网络号，设置虚拟网卡的IP地址（可以和上图的IP地址不一样，你只需要确保物理计算机的网卡、虚拟机的网卡、本协议栈的虚拟网卡这三张网卡保持在同一个网段内即可）。

.. note:: 
   **怎么样查看IP地址？**

   在Linux虚拟机环境下，打开terminal，输入ip addr命令，如下图所示。

.. image:: ip.png

.. hint:: 
   在调试之前，必须修改include/config.h头文件中的NET_IF_IP宏定义，即需要自定义网卡的IP地址。该IP地址的网络号必须与你自己电脑中真实网卡的 **网络号** 一致（也就是和真实网卡处于同一个网段内）。

   注意：此处是 **网络号** 要和真实网卡一致，以确保它们处在同一个网段内， **不是将自定义网卡（虚拟网卡）的IP地址设置成真实网卡的IP地址** 。

.. image:: vscode5.png   

VScode的安装和配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

推荐同学们使用VSCode自带的调试工具，在Windows环境下远程调试Linux下的代码，这样就不需要在物理机上编辑代码在拷贝过去手动运行（或者在虚拟机记事本中编写代码），这将节省大量的时间。

打开VScode，在左侧边栏的扩展商店中搜索并安装以下扩展：

1. Chinese (Simplified) Language Pack for Visual Studio Code
#. Remote-SSH
#. C/C++
#. CMake Tools

安装完成后重启 VSCode，在左侧边栏的远程资源管理器工具栏中点击 + 号，在顶部输入 ssh root@XX.XX.XX.XX（改为虚拟机 ip 地址）命令并一路回车。此时在工具栏中可以看到新添加的远程主机。在远程电脑上点击连接，输入密码后将会打开新窗口连接到远
程主机中。在连接到远程主机之后，需要在再次搜索并安装上述扩展，将其安装在远程主机上。C/C++ 扩展在安装后可能需要一段时间来下载依赖文件。

.. hint:: 
   在本实验中，VSCode 必须以 **root身份登录到远程主机**  中，否则将无法使用调试工具调试工程，因为本工程需要 root 权限操作网卡。

点击打开文件夹打开工程目录后即可进行代码操作（确保 CMakeLists.txt 处于当前根目录中）。此时可以在左侧边栏看到 CMake 工具栏。

在配置好 VSCode 环境之后，即可使用 CMake 工具栏完成编译和调试操作。

每次打开工程后首先需要配置 CMake，点击工具栏中的 Configure All Projects 按钮对当前工程进行配置，首次配置会弹出选择编译工具的提示，选择带有 linux 的一项。配置完成后，在工具栏中出现了本次实验的工程 net，在工程 net 中右键点击想要编译的程序（其中，main是UDP实验中使用的测试程序），即可编译、调试、运行该程序。

.. image:: vscode6.png 

.. note:: 
   如果出现调试无法启动的情况，将lauch.json 的 program 项改为 ${workspaceFolder}/build/ **你要编译的程序名** ，args 项也需要改成testing/data/ **你要编译的程序名** 。

以下是 **eth_in** 调试的完整的launch.json，大家可以参考下面的来修改：

.. code-block:: json
   :linenos:

    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "(gdb) Launch",
                "type": "cppdbg",
                "request": "launch",
                "program": "${workspaceFolder}/build/eth_in",
                "args": ["testing/data/eth_in"],
                "stopAtEntry": false,
                "cwd": "${workspaceFolder}",
                "environment": [],
                "externalConsole": false,
                "MIMode": "gdb",
                "setupCommands": [
                    {
                        "description": "Enable pretty-printing for gdb",
                        "text": "-enable-pretty-printing",
                        "ignoreFailures": true
                    }
                ],
                "preLaunchTask": "build"
            }
        ]
    }


