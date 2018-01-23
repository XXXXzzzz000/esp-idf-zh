FAT 文件系统的支持
======================

ESP-IDF 使用 `FatFs <http://elm-chan.org/fsw/ff/00index_e.html>`_ 库作为 FAT 文件系统。FatFs 库位于 ``fatfs`` 组件内。尽管它可以被直接访问，它的许多功能都可以通过 VFS 访问，即使用标准 C 库和 POSIX API。

此外，FatFs 已被修改，支持运行时可插拔磁盘 IO 层。这能让 FatFs 在运行时映射为物理磁盘。

FatFs with VFS
--------------------

头文件 ``esp_vfs_fat.h`` 中定义的函数可以将 FatFs 与 VFS 连接起来。函数 ``esp_vfs_fat_register`` 分配了一个 ``FATFS`` 结构，并在 VSF 中注册了一个所给路径前缀。随后对以这个前缀作为开始的文件的操作将会被转发给 FatFs 的 API。函数 ``esp_vfs_fat_unregister_path`` 用于删除

function deletes the registration with VFS, and frees the ``FATFS`` structure.

Most applications will use the following flow when working with ``esp_vfs_fat_`` functions:

1. Call ``esp_vfs_fat_register``, specifying path prefix where the filesystem has to be mounted (e.g. ``"/sdcard"``, ``"/spiflash"``), FatFs drive number, and a variable which will receive a pointer to ``FATFS`` structure.

2. Call ``ff_diskio_register`` function to register disk IO driver for the drive number used in step 1.

3. Call ``f_mount`` function (and optionally ``f_fdisk``, ``f_mkfs``) to mount the filesystem using the same drive number which was passed to ``esp_vfs_fat_register``. See FatFs documentation for more details.

4. Call POSIX and C standard library functions to open, read, write, erase, copy files, etc. Use paths starting with the prefix passed to ``esp_vfs_register`` (such as ``"/sdcard/hello.txt"``).

5. Optionally, call FatFs library functions directly. Use paths without a VFS prefix in this case (``"/hello.txt"``).

6. Close all open files.

7. Call ``f_mount`` function for the same drive number, with NULL ``FATFS*`` argument, to unmount the filesystem.

8. Call ``ff_diskio_register`` with NULL ``ff_diskio_impl_t*`` argument and the same drive number.

9. Call ``esp_vfs_fat_unregister_path`` with the path where the file system is mounted to remove FatFs from VFS, and free the ``FATFS`` structure allocated on step 1.

Convenience functions, ``esp_vfs_fat_sdmmc_mount`` and ``esp_vfs_fat_sdmmc_unmount``, which wrap these steps and also handle SD card initialization, are described in the next section. 

.. doxygenfunction:: esp_vfs_fat_register
.. doxygenfunction:: esp_vfs_fat_unregister_path


Using FatFs with VFS and SD cards
---------------------------------

``esp_vfs_fat.h`` header file also provides a convenience function to perform steps 1–3 and 7–9, and also handle SD card initialization: ``esp_vfs_fat_sdmmc_mount``. This function does only limited error handling. Developers are encouraged to look at its source code and incorporate more advanced versions into production applications. ``esp_vfs_fat_sdmmc_unmount`` function unmounts the filesystem and releases resources acquired by ``esp_vfs_fat_sdmmc_mount``.

.. doxygenfunction:: esp_vfs_fat_sdmmc_mount
.. doxygenstruct:: esp_vfs_fat_mount_config_t
    :members:
.. doxygenfunction:: esp_vfs_fat_sdmmc_unmount

FatFS disk IO layer
-------------------

FatFs has been extended with an API to register disk IO driver at runtime.

Implementation of disk IO functions for SD/MMC cards is provided. It can be registered for the given FatFs drive number using ``ff_diskio_register_sdmmc`` function.

.. doxygenfunction:: ff_diskio_register
.. doxygenstruct:: ff_diskio_impl_t
    :members:
.. doxygenfunction:: ff_diskio_register_sdmmc

