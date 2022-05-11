Ubuntu搭建邮件服务器
================================================

.. toctree::
   :hidden:
   :maxdepth: 5

本文档参考：https://blog.csdn.net/oolocal/article/details/52861583

准备工作
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
你需要提前安装好ubnutu。本文档提供的安装步骤是在20.04版本上完成的，其他版本的安装步骤大同小异。

安装Postfix
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Step1：** 更新本地apt包：

.. code-block:: console
   :linenos:

   sudo apt-get update

如果更新太慢了，可以切换国内更新源（切换更新源可自行百度）

**Step2：** 安装Postfix：

.. code-block:: console
   :linenos:

   sudo DEBIAN_PRIORITY=low apt install postfix

安装过程有好些提示，我们是这样选择的：

**General type of mail configuration?:**  选择  Internet Site 

**System mail name:**  这里填hitsz-lab.com， 以后你用户user的邮箱就是user@hitsz-lab.com。

**Root and postmaster mail recipient:**  比如你的ubuntu用户名是cs，那就填 cs，这样根用户等的邮箱都是cs接收。

**Other destinations to accept mail for:** 新增hitsz-lab.com。

**Force synchronous updates on mail queue?:** 选择 No

**Local networks:** 缺省就好。

**Mailbox size limit:** 邮箱限定封数，0则是不限制。

**Local address extension character:**  + 就是加号，不改。

**Internet protocols to use:** 选All

选择错了也没关系，可以用下面命令修改：

.. code-block:: console
   :linenos:

   sudo dpkg-reconfigure postfix

重启动 Postfix 使更改生效：

.. code-block:: console
   :linenos:

   sudo systemctl restart postfix

打开防火墙：

.. code-block:: console
   :linenos:

   sudo ufw allow Postfix

发送测试邮件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Postfix在安装时，会同时安装一个sendmail的程序（/usr/sbin/sendmail）。你可以用这个sendmail二进制程序向你的Gmail邮箱发送一封测试邮件。在服务器上输入下面的命令：

.. code-block:: console
   :linenos:

   echo "test email" | sendmail cs@hitsz-lab.com

这是一条很简单的命令， sendmail从标准输入读取到test email，将test email作为邮件正文，然后发送到hitsz-lab.com邮箱。现在你可以查看你的hitsz-lab.com邮箱，应该会看见你的测试邮件。尽管我们没有指明发件人地址，但Postfix会自动将你的域名添加到发送人地址中。每个用户的邮件保存在/var/spool/mail/<username>和/var/mail/<username>文件中。如果你不知道收件箱保存在哪里，运行这条命令：

.. code-block:: console
   :linenos:
   
   postconf mail_spool_directory

.. image:: install-1.png


使用mail程序来发送邮件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
sendmail的功能非常有限，现在让我们来安装一个命令行邮箱客户端。

.. code-block:: console
   :linenos:

   sudo apt-get install mailutils

使用mail发送邮件的命令为

.. code-block:: console
   :linenos:

   mail cs@hitsz-lab.com

.. image:: install-2.png

输入主题和正文后，按Ctrl+D来发送邮件。

要查看收件箱，输入mail就行了。

.. code-block:: console
   :linenos:

   mail

以下是用mail管理收件箱的操作方法。

1. 要查看第一封邮件，输入数字1。如果邮件只显示了一半，按Enter键来显示剩下的消息。
2. 将所有邮件从第一封排序，输入h。
3. 要显示最后一屏邮件，输入h$或z。
4. 阅读下一封邮件，输入n。
5. 删除第一封邮件，输入d 1。
6. 删除第一封，第二封和第四封邮件，输入d 1 2 4。
7. 删除前10封邮件，输入d 1-10。
8. 回复第1封邮件，输入reply 1。
9. 退出mail程序，输入q或x。

如果你按q来退出mail程序，那么已经阅读过的邮件将会从/var/mail/<username>移动到/home/<username>/mbox文件中。这意味着其他邮箱客户端将不能阅读这些邮件。如果你不想移动已经阅读的邮件，输入x退出mail程序。