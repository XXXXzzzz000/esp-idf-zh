Wi-Fi
=====

<<<<<<< HEAD
概述
--------
=======
Introduction
------------
>>>>>>> master

The WiFi libraries provide support for configuring and monitoring the ESP32 WiFi networking functionality. This includes configuration for:

- Station mode (aka STA mode or WiFi client mode). ESP32 connects to an access point.
- AP mode (aka Soft-AP mode or Access Point mode). Stations connect to the ESP32.
- Combined AP-STA mode (ESP32 is concurrently an access point and a station connected to another access point).

<<<<<<< HEAD
应用程序示例
-------------------

示例代码 `esp-idf-template <https://github.com/espressif/esp-idf-template>`_ 展示了如何将 ESP32 模块连接到 AP。

API 参考手册
-------------

头文件
^^^^^^^^^^^^

  * :component_file:`esp32/include/esp_wifi.h`

宏
------
=======
- Various security modes for the above (WPA, WPA2, WEP, etc.)
- Scanning for access points (active & passive scanning).
- Promiscuous mode monitoring of IEEE802.11 WiFi packets.

Application Examples
--------------------

See :example:`wifi` directory of ESP-IDF examples that contains the following applications:

* Simple application showing how to connect ESP32 module to an Access Point - `esp-idf-template <https://github.com/espressif/esp-idf-template>`_.
>>>>>>> master

* Using power save mode of Wi-Fi - :example:`wifi/power_save`.


<<<<<<< HEAD
类型定义
----------------

.. doxygentypedef:: wifi_promiscuous_cb_t
.. doxygentypedef:: esp_vendor_ie_cb_t

函数
---------
=======
API Reference
-------------
>>>>>>> master

.. include:: /_build/inc/esp_wifi.inc
.. include:: /_build/inc/esp_wifi_types.inc


