<<<<<<< HEAD
*************************************
Linux 平台工具链的标准设置
=======
﻿*************************************
Standard Setup of Toolchain for Linux
>>>>>>> master
*************************************


安装前提
=====================

要编译 ESP-IDF，你需要先安装如下的软件包。

- CentOS 7::

    sudo yum install git wget make ncurses-devel flex bison gperf python pyserial

- Ubuntu and Debian::

    sudo apt-get install git wget make libncurses-dev flex bison gperf python python-serial

- Arch::

    sudo pacman -S --needed gcc git make ncurses flex bison gperf python2-pyserial


工具链的设置
===============

Linux 版的 ESP32 工具链可以从 Espressif 的网站下载：

- for 64-bit Linux:

  https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz

- for 32-bit Linux:

  https://dl.espressif.com/dl/xtensa-esp32-elf-linux32-1.22.0-80-g6c4433a-5.2.0.tar.gz

<<<<<<< HEAD
下载完成后，将它解压到 ``~/esp`` 目录 ::
=======
1.  Download this file, then extract it in ``~/esp`` directory::
>>>>>>> master

        mkdir -p ~/esp
        cd ~/esp
        tar -xzf ~/Downloads/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz

.. _setup-linux-toolchain-add-it-to-path:

<<<<<<< HEAD
工具链将会被解压到 ``~/esp/xtensa-esp32-elf/`` 目录。

要使用工具链，你还需要在 ``~/.bash_profile`` 文件中更新环境变量 ``PATH``。要使 ``xtensa-esp32-elf`` 在所有的终端会话中有效，需要将下面这一行代码添加到你的 ``~/.bash_profile`` 文件中 ::
=======
2.  The toolchain will be extracted into ``~/esp/xtensa-esp32-elf/`` directory.

    To use it, you will need to update your ``PATH`` environment variable in ``~/.profile`` file. To make ``xtensa-esp32-elf`` available for all terminal sessions, add the following line to your ``~/.profile`` file::

        export PATH="$PATH:$HOME/esp/xtensa-esp32-elf/bin"

    Alternatively, you may create an alias for the above command. This way you can get the toolchain only when you need it. To do this, add different line to your ``~/.profile`` file::

        alias get_esp32='export PATH="$PATH:$HOME/esp/xtensa-esp32-elf/bin"'

    Then when you need the toolchain you can type ``get_esp32`` on the command line and the toolchain will be added to your ``PATH``.

    .. note::
>>>>>>> master

        If you have ``/bin/bash`` set as login shell, and both ``.bash_profile`` and ``.profile`` exist, then update ``.bash_profile`` instead.

<<<<<<< HEAD
可选地，你也可以给上面的命令创建一个别名。这样的好处是，你只在需要使用它的时候才获取工具链。你只需要将下面这行代码添加到 ``~/.bash_profile`` 文件中即可 ::
=======
3.  Log off and log in back to make the ``.profile`` changes effective. Run the following command to verify if ``PATH`` is correctly set::
>>>>>>> master

        printenv PATH

<<<<<<< HEAD
然后，当你需要使用工具链时，在命令行输入 ``get_esp32``，然后工具链会自动添加到你的 ``PATH``中。
=======
    You are looking for similar result containing toolchain's path at the end of displayed string::

        $ printenv PATH
        /home/user-name/bin:/home/user-name/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/user-name/esp/xtensa-esp32-elf/bin

    Instead of ``/home/user-name`` there should be a home path specific to your installation.


Permission issues /dev/ttyUSB0
------------------------------

With some Linux distributions you may get the ``Failed to open port /dev/ttyUSB0`` error message when flashing the ESP32. :ref:`This can be solved by adding the current user to the dialout group<linux-dialout-group>`.
>>>>>>> master


Arch Linux 用户
----------------

在 Arch Linux 中运行预编译的 gdb(xtensa-esp32-elf-gdb) 需要　ncurses 5，但是　Arch 使用的是　ncurses 6。

在 AUR_ 中有可用于本地和 lib32 配置的后向兼容库：

- https://aur.archlinux.org/packages/ncurses5-compat-libs/
- https://aur.archlinux.org/packages/lib32-ncurses5-compat-libs/

<<<<<<< HEAD
可选地，你也可以使用 crosstool-NG 编译一个链接 ncurses 6 的 gdb。
=======
Before installing these packages you might need to add the author's public key to your keyring as described in the "Comments" section at the links above.

Alternatively, use crosstool-NG to compile a gdb that links against ncurses 6.
>>>>>>> master

后续步骤
==========

要继续设置开发环境，请参考 :ref:`get-started-get-esp-idf` 一节。

修改文档
=================

.. toctree::
    :maxdepth: 1

    linux-setup-scratch


.. _AUR: https://wiki.archlinux.org/index.php/Arch_User_Repository
