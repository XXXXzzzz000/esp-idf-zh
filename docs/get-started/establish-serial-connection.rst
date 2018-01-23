与 ESP32 建立串口连接
======================================

本节用于说明如何在 ESP32 和 PC 之间建立串口连接。


将 ESP32 连接到 PC
--------------------

使用 USB 线将 ESP32 板子和 PC 连接在一起。如果你的设备驱动没有自动安装，请先确认你 ESP32 板子（或者外部转换器 dongle）上面的 USB 转串口芯片的型号，然后在互联网上搜索对应的驱动并将其安装好。

下面的链接包含乐鑫生产的 ESP32 开发板的驱动程序：

* ESP32-PICO-KIT and ESP32-DevKitC - `CP210x USB to UART Bridge VCP Drivers <https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers>`_

* ESP32-WROVER-KIT and ESP32 Demo Board - `FTDI Virtual COM Port Drivers <http://www.ftdichip.com/Drivers/D2XX.htm>`_

Above drivers are primarily for reference. They should already be bundled with the operating system and installed automatically once one of listed boards is connected to the PC.


在 Windows 上查看端口
---------------------

<<<<<<< HEAD
在 Windows 设备管理器中查看系统所识别到的 COM 端口。断开 ESP32 并重新连接，看看哪个端口从列表中消失了然后又显示出来了。
=======
Check the list of identified COM ports in the Windows Device Manager. Disconnect ESP32 and connect it back, to verify which port disappears from the list and then shows back again.
>>>>>>> master

下图显示了 ESP32 DevKitC 和 ESP32 WROVER KIT 的串口

.. figure:: ../_static/esp32-devkitc-in-device-manager.png
    :align: center
    :alt: USB to UART bridge of ESP32-DevKitC in Windows Device Manager
    :figclass: align-center

    USB to UART bridge of ESP32-DevKitC in Windows Device Manager

.. figure:: ../_static/esp32-wrover-kit-in-device-manager.png
    :align: center
    :alt: Two USB Serial Ports of ESP-WROVER-KIT in Windows Device Manager
    :figclass: align-center

    Two USB Serial Ports of ESP-WROVER-KIT in Windows Device Manager


在 Linux 和 MacOS 上查看端口
-----------------------------

要查看 ESP32 板子（或外部转换器）上面的串口的设备名，运行下面的命令两次，第一次运行前将板子/dognle断开，第二次运行前再将其插上。第二次出现的端口号就是你需要的：

Linux ::

    ls /dev/tty*

MacOS ::

    ls /dev/cu.*


.. _linux-dialout-group:

Adding user to ``dialout`` on Linux
-----------------------------------

The currently logged user should have read and write access the serial port over USB. On most Linux distributions, this is done by adding the user to ``dialout`` group with the following command::

    sudo usermod -a -G dialout $USER

Make sure you re-login to enable read and write permissions for the serial port. 


验证串口通信
------------------------

<<<<<<< HEAD
验证串口通信是可选的。你可以使用一个串口终端程序完成它。在这个例子中，我们使用的是 `PuTTY SSH Client <http://www.putty.org/>`_ ，它同时支持 Linux 和 Windows。你也可以使用其它串口工具，并设置如下的参数。

运行终端，设置端口号，波特率 115200，数据位 8，停止位 1，奇偶 N。下面分别是在 Windows 和 Linux 下面配置这些传输参数（简单的描述就是 115200-8-1-N）的例子。请一定选择你上面说识别出来的端口号。
=======
Now verify that the serial connection is operational. You can do this using a serial terminal program. In this example we will use `PuTTY SSH Client <http://www.putty.org/>`_ that is avilable for both Windows and Linux. You can use other serial program and set communication parameters like below.

Run terminal, set identified serial port, baud rate = 115200, data bits = 8, stop bits = 1, and parity = N. Below are example screen shots of setting the port and such transmission parameters (in short described as  115200-8-1-N) on Windows and Linux. Remember to select exactly the same serial port you have identified in steps above.
>>>>>>> master

.. figure:: ../_static/putty-settings-windows.png
    :align: center
    :alt: Setting Serial Communication in PuTTY on Windows
    :figclass: align-center

    在 Windows 设置 PuTTY 的串口通信

.. figure:: ../_static/putty-settings-linux.png
    :align: center
    :alt: Setting Serial Communication in PuTTY on Linux
    :figclass: align-center

    在 Linux 设置 PuTTY 的串口通信


然后在终端中打开串口，并查看是否有 ESP32 的消息打印出来。消息的内容是跟你加载到 ESP32 中的程序有关的，例如：

.. highlight:: none

::

    ets Jun  8 2016 00:22:57

    rst:0x5 (DEEPSLEEP_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
    ets Jun  8 2016 00:22:57

    rst:0x7 (TG0WDT_SYS_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
    configsip: 0, SPIWP:0x00
    clk_drv:0x00,q_drv:0x00,d_drv:0x00,cs0_drv:0x00,hd_drv:0x00,wp_drv:0x00
    mode:DIO, clock div:2
    load:0x3fff0008,len:8
    load:0x3fff0010,len:3464
    load:0x40078000,len:7828
    load:0x40080000,len:252
    entry 0x40080034
    I (44) boot: ESP-IDF v2.0-rc1-401-gf9fba35 2nd stage bootloader
    I (45) boot: compile time 18:48:10

    ...

<<<<<<< HEAD
如果你看到人类可识别的打印消息，表示串口连接工作正常，你可以继续安装并将应用程序下载到 ESP32 中。


.. note::

    验证串口通信后，请先关闭串口终端。在下一步中，我们将会使用另一个应用程序进行下载。当串口打开时，这个应用程序就无法串口。

如果你是从 :ref:`get-started-connect`跳转到此处的，点击链接 :ref:`get-started-configure` 回到之前的章节。
=======
If you see some legible log, it means serial connection is working and you are ready to proceed with installation and finally upload of application to ESP32.

.. note::

   For some serial port wiring configurations, the serial RTS & DTR pins need to be disabled in the terminal program before the ESP32 will boot and produce serial output. This depends on the hardware itself, most development boards (including all Espressif boards) *do not* have this issue. The issue is present if RTS & DTR are wired directly to the EN & GPIO0 pins. See the `esptool documentation`_ for more details.

.. note::

   Close serial terminal after verification that communication is working. In next step we are going to use another application to upload ESP32. This application will not be able to access serial port while it is open in terminal.

If you got here from section :ref:`get-started-connect` when installing s/w for ESP32 development, then go back to section :ref:`get-started-configure`.


.. _esptool documentation: https://github.com/espressif/esptool/wiki/ESP32-Boot-Mode-Selection#automatic-bootloader
>>>>>>> master
