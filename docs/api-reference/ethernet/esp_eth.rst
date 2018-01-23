以太网
========

应用程序示例
-------------------

以太网示例 :example:`ethernet/ethernet`.

<<<<<<< HEAD
API 参考手册
-------------

头文件
^^^^^^^^^^^^

  * :component_file:`ethernet/include/esp_eth.h`
    * :component_file:`ethernet/include/phy/phy.h`

PHY 接口
^^^^^^^^^^^^^^
=======
PHY Interfaces
--------------
>>>>>>> master

PHY 模块通过配置所给 PHY 的结构体 eth_config_t 进行配置。

头部包含一个默认的配置结构体。这些默认配置中的某些成员在被用于一个特殊的 PHY 硬件配置之前需要被覆盖或重置。查看以太网的示例代码可以了解这是如何完成的。

  * :component_file:`ethernet/include/eth_phy/phy.h` (common)
  * :component_file:`ethernet/include/eth_phy/phy_tlk110.h`
  * :component_file:`ethernet/include/eth_phy/phy_lan8720.h`

PHY Configuration Constants
^^^^^^^^^^^^^^^^^^^^^^^^^^^

<<<<<<< HEAD
类型定义
^^^^^^^^^^^^^^^^
=======
.. doxygenvariable:: phy_tlk110_default_ethernet_config
.. doxygenvariable:: phy_lan8720_default_ethernet_config
>>>>>>> master


<<<<<<< HEAD
枚举
^^^^^^^^^^^^
=======
API Reference - Ethernet
------------------------
>>>>>>> master

.. include:: /_build/inc/esp_eth.inc

<<<<<<< HEAD
结构体
^^^^^^^^^^
=======
API Reference - PHY Common
--------------------------
>>>>>>> master

.. include:: /_build/inc/phy.inc

API Reference - PHY TLK110
--------------------------

<<<<<<< HEAD
函数
^^^^^^^^^
=======
.. include:: /_build/inc/phy_tlk110.inc
>>>>>>> master

API Reference - PHY LAN8720
---------------------------

.. include:: /_build/inc/phy_lan8720.inc

<<<<<<< HEAD
PHY 配置常量
^^^^^^^^^^^^^^^^^^^^^^^^^^^
=======
>>>>>>> master

