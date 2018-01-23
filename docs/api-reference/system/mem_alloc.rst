<<<<<<< HEAD
内存(Memory)分配
====================
=======
Heap Memory Allocation
======================
>>>>>>> master

.. Hint:: 

  译注：本节以及后面 `深度睡眠` 一节中的 Memory 都翻译为内存的，但是实际情况可能是指的存储器，其中细节请自己体会。
  
概述
--------

<<<<<<< HEAD
ESP32 有多种 RAM。本质上，它包含 IRAM、DRAM 和可以同时用于这两者的 RAM。此外，还可以将外部 SPI flash 连接到 ESP32；可以使用 flash cache 将它的内存集成到 ESP32 的内存映射中。

为了利用这些所有的内存，esp-idf 包含了一个内存分配器。基本上，如果你想要内存具有某一属性（例如，DMA-capable、被某个特定的 PID 访问、或者执行代码的能力），你可以创建一个所需功能 OR 掩码并将它传递给 pvPortMallocCaps。例如，内部分配内存的常规 malloc 代码使用 ```pvPortMallocCaps(size, MALLOC_CAP_8BIT)```，这样就可以以字节为单位获取内存数据。

因为 malloc 也是使用的这个分配系统，所以使用 pvPortMallocCaps 分配的内存也可以通过调用标准函数 ```free()``` 进行释放。

本质上，这个分配器被分为两部分。FreeRTOS 目录中的分配器可以从标记的（tagged）区域分配内存：一个标记（tag）是一个整数值，空闲内存的每个区域都有一个标记。esp32 相关的代码使用一些特殊的标记来初始化这些区域，并且包含一些逻辑，这些逻辑可以根据用户所给的功能选择对应功能的标记。尽管这些 API 是公共的，但是还这些标记只能用于这两部分直接通信，而不能直接使用。
=======
The ESP32 has multiple types of RAM. Internally, there's IRAM, DRAM as well as RAM that can be used as both. It's also
possible to connect external SPI RAM to the ESP32 - external RAM can be integrated into the ESP32's memory map using
the flash cache.

For most purposes, the standard libc ``malloc()`` and ``free()`` functions can be used for heap allocation without any
issues.

However, in order to fully make use of all of the memory types and their characteristics, esp-idf also has a
capabilities-based heap memory allocator. If you want to have memory with certain properties (for example, DMA-capable
memory, or executable memory), you can create an OR-mask of the required capabilities and pass that to
:cpp:func:`heap_caps_malloc`. For instance, the standard ``malloc()`` implementation internally allocates memory via
``heap_caps_malloc(size, MALLOC_CAP_8BIT)`` in order to get data memory that is byte-addressable.

Because malloc uses this allocation system as well, memory allocated using :cpp:func:`heap_caps_malloc` can be freed by calling
the standard ``free()`` function.

The "soc" component contains a list of memory regions for the chip, along with the type of each memory (aka its tag) and the associated capabilities for that memory type. On startup, a separate heap is initialised for each contiguous memory region. The capabilities-based allocator chooses the best heap for each allocation, based on the requested capabilities.
>>>>>>> master

Special Uses
------------

<<<<<<< HEAD
如果某个内存结构只能以 32 比特为单位进行访问（例如整数数组或指针数组），则在分配是可以使用 MALLOC_CAP_32BIT 标志。这允许分配器分发 IRAM 内存；一些在常规 malloc() 中不能做的事儿会被调用。这样有助于是哟个 ESP32 中的所有有效内存。
=======
DMA-Capable Memory
^^^^^^^^^^^^^^^^^^

Use the MALLOC_CAP_DMA flag to allocate memory which is suitable for use with hardware DMA engines (for example SPI and I2S). This capability flag excludes any external PSRAM.

32-Bit Accessible Memory
^^^^^^^^^^^^^^^^^^^^^^^^

If a certain memory structure is only addressed in 32-bit units, for example an array of ints or pointers, it can be
useful to allocate it with the MALLOC_CAP_32BIT flag. This also allows the allocator to give out IRAM memory; something
which it can't do for a normal malloc() call. This can help to use all the available memory in the ESP32.
>>>>>>> master

Memory allocated with MALLOC_CAP_32BIT can *only* be accessed via 32-bit reads and writes, any other type of access will
generate a fatal LoadStoreError exception.

API Reference - Heap Allocation
-------------------------------

.. include:: /_build/inc/esp_heap_caps.inc

Heap Tracing & Debugging
------------------------

<<<<<<< HEAD
API 参考手册
-------------

头文件
^^^^^^^^^^^^
=======
The following features are documented on the :doc:`Heap Memory Debugging </api-reference/system/heap_debug>` page:

- :ref:`Heap Information <heap-information>` (free space, etc.)
- :ref:`Heap Corruption Detection <heap-corruption>`
- :ref:`Heap Tracing <heap-tracing>` (memory leak detection, monitoring, etc.)
>>>>>>> master

API Reference - Initialisation
------------------------------

.. include:: /_build/inc/esp_heap_caps_init.inc

<<<<<<< HEAD
宏
^^^^^^
=======
Implementation Notes
--------------------
>>>>>>> master

Knowledge about the regions of memory in the chip comes from the "soc" component, which contains memory layout information for the chip.

<<<<<<< HEAD
类型定义
^^^^^^^^^^^^^^^^
=======
Each contiguous region of memory contains its own memory heap. The heaps are created using the `multi_heap <API Reference - Multi Heap API>`_ functionality. multi_heap allows any contiguous region of memory to be used as a heap.
>>>>>>> master

The heap capabilities allocator uses knowledge of the memory regions to initialize each individual heap. When you call a function in the heap capabilities API, it will find the most appropriate heap for the allocation (based on desired capabilities, available space, and preferences for each region's use) and then call the multi_heap function to use the heap situation in that particular region.

API Reference - Multi Heap API
------------------------------

<<<<<<< HEAD
函数
^^^^^^^^^
=======
(Note: The multi heap API is used internally by the heap capabilities allocator. Most IDF programs will never need to call this API directly.)
>>>>>>> master

.. include:: /_build/inc/multi_heap.inc
