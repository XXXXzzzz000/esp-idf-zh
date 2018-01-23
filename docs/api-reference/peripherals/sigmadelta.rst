Sigma-delta Modulation
======================

<<<<<<< HEAD
概述
--------
=======
Introduction
------------
>>>>>>> master

ESP32 has a second-order sigma-delta modulation module. This driver configures the channels of the sigma-delta module.

<<<<<<< HEAD
应用程序示例
-------------------

Sigma-delta Modulation example: :example:`peripherals/sigmadelta`.

API 参考手册
-------------

头文件
^^^^^^^^^^^^

  * :component_file:`driver/include/driver/sigmadelta.h`
=======
Functionality Overview 
----------------------
>>>>>>> master

There are eight independent sigma-delta modulation channels identified with :cpp:type:`sigmadelta_channel_t`. Each channel is capable to output the binary, hardware generated signal with the sigma-delta modulation.

<<<<<<< HEAD
宏
^^^^^^
=======
Selected channel should be set up by providing configuration parameters in :cpp:type:`sigmadelta_config_t` and then applying this configuration with :cpp:func:`sigmadelta_config`.
>>>>>>> master

Another option is to call individual functions, that will configure all required parameters one by one:

<<<<<<< HEAD
类型定义
^^^^^^^^^^^^^^^^
=======
* **Prescaler** of the sigma-delta generator - :cpp:func:`sigmadelta_set_prescale`
* **Duty** of the output signal - :cpp:func:`sigmadelta_set_duty`
* **GPIO pin** to output modulated signal - :cpp:func:`sigmadelta_set_pin`
>>>>>>> master

The range of the 'duty' input parameter of :cpp:func:`sigmadelta_set_duty` is from -128 to 127 (eight bit signed integer). If zero value is set, then the output signal's duty will be about 50%, see description of :cpp:func:`sigmadelta_set_duty`.

<<<<<<< HEAD
枚举
^^^^^^^^^^^^

.. doxygenenum:: sigmadelta_channel_t

结构体
^^^^^^^^^^

.. doxygenstruct:: sigmadelta_config_t
    :members:


函数
^^^^^^^^^
=======
Application Example
-------------------

Sigma-delta Modulation example: :example:`peripherals/sigmadelta`.
>>>>>>> master

API Reference
-------------

.. include:: /_build/inc/sigmadelta.inc
