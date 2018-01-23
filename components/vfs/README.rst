虚拟文件系统(VFS)组件
============================

概述
--------

虚拟文件系统（VFS）组件为驱动程序提供了统一的接口，可以执行类似文件对象的操作。这既可以是一个真实文件系统（FAT、SPIFFS 等），也可以是暴露了类似文件接口的设备驱动程序。

该组件允许 C 库函数（例如 fopen、fprintf）与文件系统驱动程序一起工作。在顶层，每个文件系统驱动程序都关联了某些路径前缀。当某个 C 库函数需要打开文件时，VFS 组件会查找与文件的路径相关的文件系统驱动，然后将调用转给那个驱动。


例如，你可以通过前缀 ``/fat`` 注册一个 FAT 文件系统，然后调用 ``fopen("/fat/file.txt", "w")``。VFS 组件将会调用 FAT 驱动 ``open`` 函数，并将参数 ``/file.txt`` （以及一些 mode 标志）传递给它。随后对返回的 ``FILE*`` 文件流进行调用的 C 库函数也会被转给 FAT 驱动。

FS 注册
---------------

<<<<<<< HEAD
要注册一个 FS 驱动，应用程序需要定义一个 esp_vfs_t 结构体的实例，并初始化它里面的函数指针 ::
=======


To register an FS driver, application needs to define in instance of esp_vfs_t structure and populate it with function pointers to FS APIs:

.. highlight:: c

::
>>>>>>> master

    esp_vfs_t myfs = {
        .flags = ESP_VFS_FLAG_DEFAULT,
        .write = &myfs_write,
        .open = &myfs_open,
        .fstat = &myfs_fstat,
        .close = &myfs_close,
        .read = &myfs_read,
    };

    ESP_ERROR_CHECK(esp_vfs_register("/data", &myfs, NULL));

你可以使用的 API 依赖于 FS 驱动程序申明 API 的方式，例如 ``read``, ``write``, 等, 或 ``read_p``, ``write_p``等。

情形 1: API 函数定义时没有额外的上下文指针 (FS driver is a singleton)::

    ssize_t myfs_write(int fd, const void * data, size_t size);

    // In definition of esp_vfs_t:
        .flags = ESP_VFS_FLAG_DEFAULT,
        .write = &myfs_write,
    // ... other members initialized
<<<<<<< HEAD
    
    // 当注册 FS 时，上下文指针(第三个参数)是 NULL:
=======

    // When registering FS, context pointer (third argument) is NULL:
>>>>>>> master
    ESP_ERROR_CHECK(esp_vfs_register("/data", &myfs, NULL));

情形 2: API 函数定义时有额外的上下文指针 (FS 驱动支持多个实例)::

    ssize_t myfs_write(myfs_t* fs, int fd, const void * data, size_t size);

    // In definition of esp_vfs_t:
        .flags = ESP_VFS_FLAG_CONTEXT_PTR,
        .write_p = &myfs_write,
    // ... other members initialized
<<<<<<< HEAD
    
    // 当注册 FS 时，FS 上下文指针传递给了第三个参数
=======

    // When registering FS, pass the FS context pointer into the third argument
>>>>>>> master
    // (hypothetical myfs_mount function is used for illustrative purposes)
    myfs_t* myfs_inst1 = myfs_mount(partition1->offset, partition1->size);
    ESP_ERROR_CHECK(esp_vfs_register("/data1", &myfs, myfs_inst1));

    // 可以注册另外的实例
    myfs_t* myfs_inst2 = myfs_mount(partition2->offset, partition2->size);
    ESP_ERROR_CHECK(esp_vfs_register("/data2", &myfs, myfs_inst2));

路径
-----

每个注册的 FS 都有一个相关的前缀。这个前缀可以被看成是一个该分区的“挂载点”。

<<<<<<< HEAD
在已有的挂载点上注册另一个挂载点是不被支持的，其结果是未定义的。例如，下面是正确、支持的：
=======
In case when mount points are nested, the mount point with the longest matching path prefix is used when opening the file. For instance, suppose that the following filesystems are registered in VFS:
>>>>>>> master

- FS 1 on /data
- FS 2 on /data/static

<<<<<<< HEAD
下面这种方法 **不会正确工作**：
=======
Then:
>>>>>>> master

- FS 1 will be used when opening a file called ``/data/log.txt``
- FS 2 will be used when opening a file called ``/data/static/index.html``
- Even if ``/index.html"`` doesn't exist in FS 2, FS 1 will *not* be searched for ``/static/index.html``.

As a general rule, mount point names must start with the path separator (``/``) and must contain at least one character after path separator. However an empty mount point name is also supported, and may be used in cases when application needs to provide "fallback" filesystem, or override VFS functionality altogether. Such filesystem will be used if no prefix matches the path given.

VFS does not handle dots (``.``) in path names in any special way. VFS does not treat ``..`` as a reference to the parent directory. I.e. in the above example, using a path ``/data/static/../log.txt`` will not result in a call to FS 1 to open ``/log.txt``. Specific FS drivers (such as FATFS) may handle dots in file names differently.

打开文件时，FS 驱动只会收到文件的相对路径，例如：

- ``myfs`` 注册时的路径前缀是 ``/data``
- 应用程序调用 ``fopen("/data/config.json", ...)``
- 然后 VFS 组件将调用 ``myfs_open("/config.json", ...)``
- ``myfs`` 驱动将打开文件 ``/config.json``

VFS 不会限制文件路径的总长度，但是会限制文件路径前缀的长度，即最多为 ``ESP_VFS_PATH_MAX`` 个字符。另外，FS 驱动可能自己会对文件名长度有限制。

文件描述符
----------------

建议在文件系统驱动中使用一个小的正整数作为文件描述符。VFS 组件假设用 ``CONFIG_MAX_FD_BITS`` 比特（默认值 12）就足够表示文件描述符。

<<<<<<< HEAD
如果文件系统配置了一个文件描述符偏移选项（一个常数值），该值应当被传递到结构体 ``esp_vfs_t`` 中的 ``fd_offset`` 字段。在处理指定的文件系统的 FS 时，VFS 组件会移除这个偏移量，使其处于小的正整数的范围。

尽管由 VFS 返回给 newlib 库的文件描述符通常对应用程序不可见，但是理解下面的这些细节有助于调试。由 VFS 组件返回的文件描述符由两部分组成：FS 驱动 ID 和实际的文件描述符。由于 newlib 用 16 比特的整数来存储文件描述符，VFS 组件在存储这两部分时也受到 16 比特的限制。

较低的 ``CONFIG_MAX_FD_BITS`` 比特被用于存储基于零（zero-based）的文件描述符。如果 FS 驱动有一个非零 ``fd_offset`` 字段， 则这个 ``fd_offset`` 会减去一个在文件系统的 ``open`` 调用时获取到的 FD，然后其结果存储到 FD 的低比特。高比特用于保存该 FS 在已注册的文件系统构成的内部表格中的索引。

当 VFS 组件从 newlib 接收到一个带有文件描述符的调用时，该文件描述符会被转换成文件系统相关的文件描述符。首先，FD 的高比特用于标识文件系统。然后，FS 的 ``fd_offset`` 字段与 fd 较低的 ``CONFIG_MAX_FD_BITS`` 比特相加，然后将其相加结果传递给文件系统的驱动。
=======
While file descriptors returned by VFS component to newlib library are rarely seen by the application, the following details may be useful for debugging purposes. File descriptors returned by VFS component are composed of two parts: FS driver ID, and the actual file descriptor. Because newlib stores file descriptors as 16-bit integers, VFS component is also limited by 16 bits to store both parts.

Lower ``CONFIG_MAX_FD_BITS`` bits are used to store zero-based file descriptor. The per-filesystem FD obtained from the FS ``open`` call, and this result is stored in the lower bits of the FD. Higher bits are used to save the index of FS in the internal table of registered filesystems.

When VFS component receives a call from newlib which has a file descriptor, this file descriptor is translated back to the FS-specific file descriptor. First, higher bits of FD are used to identify the FS. Then only the lower ``CONFIG_MAX_FD_BITS`` bits of the fd are masked in, and resulting FD is passed to the FS driver.

.. highlight:: none
>>>>>>> master

::

       FD as seen by newlib                                    FD as seen by FS driver

    +-------+---------------+                               +------------------------+
    | FS id | Zero—based FD |     +-------------------------->                        |
    +---+---+------+--------+     |                          +------------------------+
        |          |              |
        |          +--------------+
        |
        |       +-------------+
        |       | Table of    |
        |       | registered  |
        |       | filesystems |
        |       +-------------+    +-------------+
        +------->  entry      +----> esp_vfs_t   |
        index   +-------------+    | structure   |
                |             |    |             |
                |             |    |             |
                +-------------+    +-------------+


标准 IO 流 (stdin, stdout, stderr)
-------------------------------------------

如果菜单配置选项 "UART for console output" 没有设置为 "None"，则 ``stdin``、``stdout`` 和 ``stderr`` 会被配置成从 UART 中读写。UART0 或 UART1 均可以用作标准 IO。默认情况下使用的是 UART0，波特率是 115200，TX 引脚是 GPIO1，RX 引脚是 GPIO3。这些参数可以在配置菜单中修改。

向 ``stdout`` 或 ``stderr`` 中写时会发送字符到 UART 的传输 FIFO。从 ``stdin`` 中读会从 UART 的接收 FIFO 中取数据。

<<<<<<< HEAD
注意，向 ``stdout`` 或 ``stderr`` 中写时会阻塞，直到所有的字符都被放到 FIFO 中；从 ``stdin`` 中读是非阻塞的。从 UART 中读的函数会获取到 FIFO 中的所有存在的字符。例如，``fscanf("%d\n", &var);`` 可能不会产生预期的结果。这个限制是临时的，且会在将 ``fcntl`` 添加到 VFS 接口后移除。
=======
By default, VFS uses simple functions for reading from and writing to UART. Writes busy-wait until all data is put into UART FIFO, and reads are non-blocking, returning only the data present in the FIFO. Because of this non-blocking read behavior, higher level C library calls, such as ``fscanf("%d\n", &var);`` may not have desired results.

Applications which use UART driver may instruct VFS to use the driver's interrupt driven, blocking read and write functions instead. This can be done using a call to ``esp_vfs_dev_uart_use_driver`` function. It is also possible to revert to the basic non-blocking functions using a call to ``esp_vfs_dev_uart_use_nonblocking``.

VFS also provides optional newline conversion feature for input and output. Internally, most applications send and receive lines terminated by LF (''\n'') character. Different terminal programs may require different line termination, such as CR or CRLF. Applications can configure this separately for input and output either via menuconfig, or by calls to ``esp_vfs_dev_uart_set_rx_line_endings`` and ``esp_vfs_dev_uart_set_tx_line_endings`` functions.


>>>>>>> master

标准流和 FreeRTOS 任务
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

<<<<<<< HEAD
``stdin``、``stdout`` 和 ``stderr`` 的 ``FILE`` 对象在所有的 FreeRTOS 任务中是共享的，但是执行这些对象的指针是存储在每个任务的 ``struct _reent`` 中的。下面的代码 ::

    fprintf(stderr, "42\n");

实际上会被（由预处理器）转换成:

    fprintf(__getreent()->_stderr, "42\n");

其中，函数 ``__getreent()`` 返回一个指向 ``struct _reent`` (:component_file:`newlib/include/sys/reent.h#L370-L417>`) 的指针。这个结构体分配在每个任务的 TCB 上。当任务被初始化时，`struct _reent`` 的成员 ``stdin``、``stdout`` 和 ``stderr`` 的值被设置为 ``_GLOBAL_REENT``（FreeRTOS 启动前的一个结构体） 的 ``_stdin``、``_stdout`` 和 ``_stderr``。
=======
``FILE`` objects for ``stdin``, ``stdout``, and ``stderr`` are shared between all FreeRTOS tasks, but the pointers to these objects are are stored in per-task ``struct _reent``. The following code:

.. highlight:: c

::

    fprintf(stderr, "42\n");

actually is translated to to this (by the preprocessor)::

    fprintf(__getreent()->_stderr, "42\n");

where the ``__getreent()`` function returns a per-task pointer to ``struct _reent`` (:component_file:`newlib/include/sys/reent.h#L370-L417`). This structure is allocated on the TCB of each task. When a task is initialized, ``_stdin``, ``_stdout`` and ``_stderr`` members of ``struct _reent`` are set to the values of ``_stdin``, ``_stdout`` and ``_stderr`` of ``_GLOBAL_REENT`` (i.e. the structure which is used before FreeRTOS is started).
>>>>>>> master

这样设计的结果：

<<<<<<< HEAD
- 通过执行 ``stdin = fopen("/dev/uart/1", "r")`` 可以为任何所给任务设置 ``stdin````stdout`` 和 ``stderr``。
- 使用 ``fclose`` 可以默认的 ``stdin``、``stdout`` 或 ``stderr`` 将关闭 ``FILE`` 流对象 — 这会影响其它所有任务。
- 如果要为新任务改变默认的 ``stdin``、``stdout`` 或 ``stderr`` 流，则在创建任务前修改 ``_GLOBAL_REENT->_stdin`` (``_stdout``, ``_stderr``)。

=======
- It is possible to set ``stdin``, ``stdout``, and ``stderr`` for any given task without affecting other tasks, e.g. by doing ``stdin = fopen("/dev/uart/1", "r")``.
- Closing default ``stdin``, ``stdout``, or ``stderr`` using ``fclose`` will close the ``FILE`` stream object — this will affect all other tasks.
- To change the default ``stdin``, ``stdout``, ``stderr`` streams for new tasks, modify ``_GLOBAL_REENT->_stdin`` (``_stdout``, ``_stderr``) before creating the task.
>>>>>>> master
