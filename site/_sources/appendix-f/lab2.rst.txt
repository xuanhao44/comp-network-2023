Lab2 常见问题
================================================

.. toctree::
   :hidden:
   :maxdepth: 5

安装完CMAKE和TDM GCC后，VSCode的CMAKE上找不到项目的可执行程序？
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如下图所示，找不到项目的可执行程序。

.. image:: lab2.png

首先，你需要先查询 "C:\\Users\\你自己电脑的用户名\\AppData\\Local\\CMakeTools\\CMakeTools-tools-kits.json"文件，该文件正常的配置应该如下图所示。

.. image:: lab2-2.png

如果你电脑中该文件的gcc不一样，比如是C:\\\TDM-GCC-64\\\bin\\\gcc.exe，那么请你到C:\\TDM-GCC-64\\bin目录下，删掉gcc.exe。然后点击VSCode最下方的“GCC图标”，重新扫描[Scan for kits]，重新选择GCC，应该选择的是C:\\TDM-GCC-64\\bin\\x86_64-w64-mingw32-gcc.exe。

.. image:: lab2-4.png

.. image:: lab2-3.png

如果还不行，删掉本工程目录下的.vscode\\settings.json文件，再重新扫描[Scan for kits]。

