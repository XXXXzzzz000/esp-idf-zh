.. include:: ../../../components/wear_levelling/README.rst

See also
--------

- :doc:`FAT 文件系统 <../../api-guides/partition-tables>`
- :doc:`分区表文档 <../../api-guides/partition-tables>`

<<<<<<< HEAD

应用程序示例
=======
Application Example
>>>>>>> master
-------------------

An example which combines wear levelling driver with FATFS library is provided in ``examples/storage/wear_levelling`` directory. This example initializes the wear levelling driver, mounts FATFS partition, and writes and reads data from it using POSIX and C library APIs. See README.md file in the example directory for more information.

High level API 参考手册
------------------------

头文件
^^^^^^^^^^^^

* :component_file:`fatfs/src/esp_vfs_fat.h`

函数
^^^^^^^^^

.. doxygenfunction:: esp_vfs_fat_spiflash_mount
.. doxygenstruct:: esp_vfs_fat_mount_config_t
    :members:
.. doxygenfunction:: esp_vfs_fat_spiflash_unmount

Mid level API 参考手册
-----------------------

<<<<<<< HEAD
头文件
^^^^^^^^^^^^

  * :component_file:`wear_levelling/include/wear_levelling.h`

函数
^^^^^^^^^

.. doxygenfunction:: wl_mount
.. doxygenfunction:: wl_unmount
.. doxygenfunction:: wl_erase_range
.. doxygenfunction:: wl_write
.. doxygenfunction:: wl_read
.. doxygenfunction:: wl_size
.. doxygenfunction:: wl_sector_size
=======
.. include:: /_build/inc/wear_levelling.inc
>>>>>>> master

