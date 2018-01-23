***********
快速入门
***********

<<<<<<< HEAD
开发 ESP32 应用程序需要准备：

* 安装有 Windows、Linux 或者 Mac 操作系统的 **PC**
* 用于编译 ESP32 **应用程序** 的 **工具链**
* **ESP-IDF** —— 包含 ESP32 的 API 和用于操作 **工具链** 的脚本
* 编写 C 语言程序的文本编辑器，例如 `Eclipse <http://www.eclipse.org/>`_
* **ESP32** 开发板
=======
This document is intended to help users set up the software environment for developement of applications using hardware based on the Espressif ESP32. Through a simple example we would like to illustrate how to use ESP-IDF (Espressif IoT Development Framework), including the menu based configuration, compiling the ESP-IDF and firmware download to ESP32 boards. 


Introduction
============

ESP32 integrates Wi-Fi (2.4 GHz band) and Bluetooth 4.2 solutions on a single chip, along with dual high performance cores, Ultra Low Power co-processor and several peripherals. Powered by 40 nm technology, ESP32 provides a robust, highly integrated platform to meet the continuous demands for efficient power usage, compact design, security, high performance, and reliability.

Espressif provides the basic hardware and software resources that help application developers to build their ideas around the ESP32 series hardware. The software development framework by Espressif is intended for rapidly developing Internet-of-Things (IoT) applications, with Wi-Fi, Bluetooth, power management and several other system features. 


What You Need
=============

To develop applications for ESP32 you need:

* **PC** loaded with either Windows, Linux or Mac operating system
* **Toolchain** to build the **Application** for ESP32
* **ESP-IDF** that essentially contains API for ESP32 and scripts to operate the **Toolchain**
* A text editor to write programs (**Projects**) in C, e.g. `Eclipse <https://www.eclipse.org/>`_
* The **ESP32** board itself and a **USB cable** to connect it to the **PC**
>>>>>>> master

.. figure:: ../_static/what-you-need.png
    :align: center
    :alt: Development of applications for ESP32
    :figclass: align-center

    为 ESP32 开发应用程序

开发环境的准备工作包括以下三部分：

1. 设置 **工具链**
2. 从 GitHub 上面获取 **ESP-IDF**
3. 安装和配置 **Eclipse**

如果你喜欢用其它的编辑器，则可以跳过最后一步。

环境设置好后，你就可以开始开发应用程序了。整个过程可以概括为如下四步：

1. 配置 **工程** 并编写代码
2. 编译 **工程** 并链接成一个 **应用程序**
3. 烧写 **应用程序** 到 **ESP32** 上面
4. 监视/调试 **应用程序**

请继续阅读下面的指令，它将带你完成这些步骤。

指导
======

如果你有下面所列举的某块 ESP32 开发板，请点击对应的链接，它会教你如何让你的板子跑起来。

.. toctree::
    :maxdepth: 1

    ESP32 DevKitC <get-started-devkitc>
    ESP-WROVER-KIT <get-started-wrover-kit>
    ESP32-PICO-KIT <get-started-pico-kit>

如果你有其它的开发板，请查看下面的内容。

.. _get-started-setup-toolchain:

设置工具链
===============

<<<<<<< HEAD
你可以完全遵循标准安装过程或者自定义你的环境，这完全依赖于你个人的经验和喜好。下面的指令用于标准安装。如果要在你自己的系统上进行设置，请移步 :ref:`get-started-customized-setup`。

.. _get-started-standard-setup:

工具链的标准设置
---------------------------

用 ESP32 进行开发最快的方法是安装预编译的工具链。请根据你的操作系选择点击对应的链接，并按照该链接中的指令进行安装。
=======
The quickest way to start development with ESP32 is by installing a prebuilt toolchain. Pick up your OS below and follow provided instructions. 
>>>>>>> master

.. toctree::
    :hidden:

    Windows <windows-setup>
    Linux <linux-setup> 
    MacOS <macos-setup> 

+-------------------+-------------------+-------------------+
| |windows-logo|    | |linux-logo|      | |macos-logo|      |
+-------------------+-------------------+-------------------+
| `Windows`_        | `Linux`_          | `Mac OS`_         |
+-------------------+-------------------+-------------------+

.. |windows-logo| image:: ../_static/windows-logo.png
    :target: ../get-started/windows-setup.html

.. |linux-logo| image:: ../_static/linux-logo.png
    :target: ../get-started/linux-setup.html

.. |macos-logo| image:: ../_static/macos-logo.png
    :target: ../get-started/macos-setup.html

.. _Windows: ../get-started/windows-setup.html
.. _Linux: ../get-started/linux-setup.html
.. _Mac OS: ../get-started/macos-setup.html

.. note::

<<<<<<< HEAD
    我们默认使用 ``~/esp`` 目录来安装预编译的工具链、ESP-IDF 和示例程序。你也可以使用其它目录，但是需要注意调整对应的命令。
    
设置完工具链后，你可以进入 :ref:`get-started-get-esp-idf` 一节。

.. highlight:: bash

.. _get-started-customized-setup:

工具链的自定义设置
-----------------------------

除了从乐鑫的网站(:ref:`get-started-standard-setup`)下载预编译的二进制工具链外，你还可以自己编译工具链。

如果你找不到需要自己编译的理由，那么最好还是使用预编译版本吧。不过，这里可能有一些你希望从源码进行编译的理由：

- 如果你想自定义工具链的编译配置选项

- 如果你想使用不同版本的 GCC，例如 4.8.5

- if you want to hack gcc or newlib or libstdc++

- 如果你很好奇，和/或你有许多闲暇时间

- 如果你不信任从互联网上面下载的二进制镜像

无论是因为何种情形，请都按照下面的指令编译你自己的工具链。

.. toctree::
    :maxdepth: 1
=======
    We are using ``~/esp`` directory to install the prebuilt toolchain, ESP-IDF and sample applications. You can use different directory, but need to adjust respective commands.

Depending on your experience and preferences, instead of using a prebuilt toolchain, you may want to customize your environment. To set up the system your own way go to section :ref:`get-started-customized-setup`.
>>>>>>> master

Once you are done with setting up the toolchain then go to section :ref:`get-started-get-esp-idf`.


.. _get-started-get-esp-idf:

获取 ESP-IDF
=============

<<<<<<< HEAD
工具链（包括用于编译和构建应用程序的程序）安装完后，你还需要 ESP32 相关的 API/库。乐鑫已经将它们放到 `ESP-IDF 仓库 <https://github.com/espressif/esp-idf>`_ 中了。
要获取这些 API/库，请打开一个控制台终端，进入某个你希望存放 ESP-IDF 的目录，然后克隆代码 ::
=======
.. highlight:: bash

Besides the toolchain (that contains programs to compile and build the application), you also need ESP32 specific API / libraries. They are provided by Espressif in `ESP-IDF repository <https://github.com/espressif/esp-idf>`_. To get it, open terminal, navigate to the directory you want to put ESP-IDF, and clone it using ``git clone`` command::
>>>>>>> master

    cd ~/esp
    git clone --recursive https://github.com/espressif/esp-idf.git

ESP-IDF 将会被下载到 ``~/esp/esp-idf``。

.. note::

    注意这里还有个 ``--recursive`` 选项。如果你克隆 ESP-IDF 时没有带这个选项，你还需要运行额外的命令来获取子模块 ::

        cd ~/esp/esp-idf
        git submodule update --init

<<<<<<< HEAD
.. note::

    在 **Windows** 平台克隆子模块时，``git clone`` 命令可能会打印一些 ``': not a valid identifier...`` 消息。这是一个 `已知问题 <https://github.com/espressif/esp-idf/issues/11>`_ ，但实际上 git clone 已经成功了，没有任何问题。
=======
>>>>>>> master

.. _get-started-setup-path:

设置 ESP-IDF 路径
=====================

工具链程序使用环境变量 ``IDF_PATH`` 来访问 ESP-IDF。这个变量应该设置在你的 PC 中，否则工程将不会编译。你可以在每次 PC 重启时手工设置。你也可以通过在 user profile 中定义 ``IDF_PATH`` 变量来永久性设置。要永久性设置，请按照 :doc:`add-idf_path-to-profile` 一节中 :ref:`Windows <add-idf_path-to-profile-windows>` 或者 :ref:`Linux and MacOS <add-idf_path-to-profile-linux-macos>` 中所指定的指令进行操作。
:ref:`Linux and MacOS <add-idf_path-to-profile-linux-macos>` in section :doc:`add-idf_path-to-profile`.


.. _get-started-start-project:

开始一个工程
===============

到了这里，你已经完成为 ESP32 编写应用程序的所有准备工作了。为了快速开始，我们这里以 IDF 的 :idf:`examples` 目录下的 :example:`get-started/hello_world` 工程为例进行说明。

将 :example:`get-started/hello_world` 拷贝到 ``~/esp`` 目录::

    cd ~/esp
    cp -r $IDF_PATH/examples/get-started/hello_world .

你可以在 ESP-IDF 的 :idf:`examples` 目录下面发现一系列的示例工程。你可以按照上面的方法将使用这些例子作为你自己的工程，并在此基础之上进行开发。

.. important::

    esp-idf 构建系统不支持在路径中存在空格。

.. _get-started-connect:

连接
=======

现在已经差不多了。在继续后续操作前，请现将 ESP32 的板子连接到 PC，然后检查 PC 所识别到的板子的串口号，看看它是否能正常通信。如果你不知道如何操作，请查看 :doc:`establish-serial-connection` 中的相关指令。请注意一下端口号，因为我们在下一步中将会用到。

.. _get-started-configure:

配置
=========

在终端窗口中，输入 ``cd ~/esp/hello_world`` 进入 ``hello_world`` 所在目录，然后启动刚工程配置工具 ``menuconfig``::

    cd ~/esp/hello_world
    make menuconfig

如果之前的步骤都正确，则会显示下面的菜单：

.. figure:: ../_static/project-configuration.png
    :align: center
    :alt: Project configuration - Home window
    :figclass: align-center

    工程配置 - 主窗口
    
在菜单中，进入 ``Serial flasher config`` > ``Default serial port`` 来配置串口（工程将会加载到该串口上）。输入回车来确认选择，选择 ``< Save >`` 来保存配置，选择 ``< Exit >`` 来退出应用程序。

下面是一些使用 ``menuconfig`` 的小技巧：

<<<<<<< HEAD
* 使用 up & down 组合键在菜单中上下移动
* 使用 Enter 键进入一个子菜单，Escape 键退出子菜单或退出整个菜单
* 输入 ``?`` 查看帮助信息，Enter 键退出帮助屏幕
* 使用空格键或 ``Y`` 和 ``N`` 键来使能(Yes) 和禁止 (No) 带有复选框 "``[*]``" 的配置项
* 当光标在某个配置项上面高亮时，输入 ``?`` 可以直接查看该项的帮助信息
* 输入 ``/`` 可以来搜索某个配置项
=======
.. note::

   On Windows, serial ports have names like COM1. On MacOS, they start with ``/dev/cu.``. On Linux, they start with ``/dev/tty``.
   (See :doc:`establish-serial-connection` for full details.)

Here are couple of tips on navigation and use of ``menuconfig``:

* Use up & down arrow keys to navigate the menu.
* Use Enter key to go into a submenu, Escape key to go out or to exit.
* Type ``?`` to see a help screen. Enter key exits the help screen.
* Use Space key, or ``Y`` and ``N`` keys to enable (Yes) and disable (No) configuration items with checkboxes "``[*]``"
* Pressing ``?`` while highlighting a configuration item displays help about that item.
* Type ``/`` to search the configuration items.
>>>>>>> master

.. note::

    如果你是 **Arch Linux** 用户，需要进入 ``SDK tool configuration`` 将 ``Python 2 interpreter`` 从 ``python`` 修改为 ``python2``。


.. _get-started-build-flash:

编译和烧写
===============

现在你可以编译和烧写应用程序了，输入 ::

    make flash

上面的命令会将应用程序、所有的 ESP-IDF 组件、通用的 bootloader、分区表编译成应用程序二进制文件，并将这些应用程序二进制文件烧写到 ESP32 的板子上面。

.. highlight:: none

::

    esptool.py v2.0-beta2
    Flashing binaries to serial port /dev/ttyUSB0 (app at offset 0x10000)...
    esptool.py v2.0-beta2
    Connecting........___
    Uploading stub...
    Running stub...
    Stub running...
    Changing baud rate to 921600
    Changed.
    Attaching SPI flash...
    Configuring flash size...
    Auto-detected Flash size: 4MB
    Flash params set to 0x0220
    Compressed 11616 bytes to 6695...
    Wrote 11616 bytes (6695 compressed) at 0x00001000 in 0.1 seconds (effective 920.5 kbit/s)...
    Hash of data verified.
    Compressed 408096 bytes to 171625...
    Wrote 408096 bytes (171625 compressed) at 0x00010000 in 3.9 seconds (effective 847.3 kbit/s)...
    Hash of data verified.
    Compressed 3072 bytes to 82...
    Wrote 3072 bytes (82 compressed) at 0x00008000 in 0.0 seconds (effective 8297.4 kbit/s)...
    Hash of data verified.

    Leaving...
    Hard resetting...

如果没有任何问题，在编译过程结束后，你将能看到类似上面的将程序加载到板子上面的消息。最后，板子将会复位，应用程序 "hello_world" 开始启动。

如果你偏向于使用 Eclipse IDE 而不是运行 ``make``，请参考 :doc:`Eclipse guide <eclipse-setup>`。


.. _get-started-build-monitor:

监视器
=======

如果要看 "hello_world" 程序是否真的在运行，输入命令 ``make monitor``。这个命令会启动 :doc:`IDF Monitor <idf-monitor>` 程序 ::

    $ make monitor
    MONITOR
    --- idf_monitor on /dev/ttyUSB0 115200 ---
    --- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
    ets Jun  8 2016 00:22:57

    rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
    ets Jun  8 2016 00:22:57
    ...

板子启动后，你就能看到 "Hello world!" 程序所打印的消息： ::

    ...
    Hello world!
    Restarting in 10 seconds...
    I (211) cpu_start: Starting scheduler on APP CPU.
    Restarting in 9 seconds...
    Restarting in 8 seconds...
    Restarting in 7 seconds...

<<<<<<< HEAD
要退出监视器，请使用快捷键 ``Ctrl+]`` 。如果要在同一个命令中执行 ``make flash`` 和 ``make monitor``，可以直接输入 ``make flash monitor``。关于监视器的更多使用细节请参考 :doc:`IDF Monitor <idf-monitor>`。
=======
To exit the monitor use shortcut ``Ctrl+]``. 

.. note::

    If instead of the messages above, you see a random garbage similar to::

        e���)(Xn@�y.!��(�PW+)��Hn9a؅/9�!�t5��P�~�k��e�ea�5�jA
        ~zY��Y(1�,1�� e���)(Xn@�y.!Dr�zY(�jpi�|�+z5Ymvp

    or monitor fails shortly after upload, your board is likely using 26MHz crystal, while the ESP-IDF assumes default of 40MHz. Exit the monitor, go back to the :ref:`menuconfig <get-started-configure>`, change :ref:`CONFIG_ESP32_XTAL_FREQ_SEL` to 26MHz, then :ref:`build and flash <get-started-build-flash>` the application again. This is found under ``make menuconfig`` under Component config --> ESP32-specific --> Main XTAL frequency.

To execute ``make flash`` and ``make monitor`` in one go, type ``make flash monitor``. Check section :doc:`IDF Monitor <idf-monitor>` for handy shortcuts and more details on using this application.

That's all what you need to get started with ESP32! 

Now you are ready to try some other :idf:`examples`, or go right to developing your own applications.


Updating ESP-IDF
================

After some time of using ESP-IDF, you may want to update it to take advantage of new features or bug fixes. The simplest way to do so is by deleting existing ``esp-idf`` folder and cloning it again, exactly as when doing initial installation described in sections :ref:`get-started-get-esp-idf`.

Another solution is to update only what has changed. This method is useful if you have slow connection to the GiHub. To do the update run the following commands::

    cd ~/esp/esp-idf
    git pull
    git submodule update --init --recursive

The ``git pull`` command is fetching and merging changes from ESP-IDF repository on GitHub. Then ``git submodule update --init --recursive`` is updating existing submodules or getting a fresh copy of new ones. On GitHub the submodules are represented as links to other repositories and require this additional command to get them onto your PC.

If you would like to use specific release of ESP-IDF, e.g. `v2.1`, run::

    cd ~/esp
    git clone https://github.com/espressif/esp-idf.git esp-idf-v2.1
    cd esp-idf-v2.1/
    git checkout v2.1
    git submodule update --init --recursive

After that remember to :doc:`add-idf_path-to-profile`, so the toolchain scripts know where to find the ESP-IDF in it's release specific location.

>>>>>>> master

相关文档
=================

.. toctree::
    :maxdepth: 1

    add-idf_path-to-profile
    establish-serial-connection
    make-project
    eclipse-setup
    idf-monitor
    toolchain-setup-scratch
