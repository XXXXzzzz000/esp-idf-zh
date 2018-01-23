看门狗
=========

概述
--------

<<<<<<< HEAD
Esp-idf 支持两种类型的看门狗：任务看门狗和中断看门狗。二者都可以通过使用 ``make menuconfig`` 进行配置和选择恰当的选项。
=======
The ESP-IDF has support for two types of watchdogs: The Interrupt Watchdog Timer
and the Task Watchdog Timer (TWDT). The Interrupt Watchdog Timer and the TWDT
can both be enabled using ``make menuconfig``, however the TWDT can also be
enabled during runtime. The Interrupt Watchdog is responsible for detecting
instances where FreeRTOS task switching is blocked for a prolonged period of
time. The TWDT is responsible for detecting instances of tasks running without
yielding for a prolonged period.
>>>>>>> master

中断看门狗
^^^^^^^^^^^^^^^^^^

中断看门狗可以确保 FreeRTOS 任务切换中断不会被阻塞太长时间。否则这是非常糟糕的，因为没有其它任务（包括相当重要的任务，比如 WiFi 任务和空转任务）能够获取到任何 CPU 时间。如果在中断被禁止时，程序运行到了无线循环，或者程序在中断中被挂起，则任务切换将会阻塞。

中断看门狗的默认行为是调用 panic handler。产生寄存器 dump 可以帮助程序员（使用 OpenOCD 或者 gdbstub）查看代码的问题。也可以通过配置 panic handler，让它直接对 CPU 进行复位，这更适合于实际产品环境。

中断看门狗被编译到定时器组 1 的赢硬件看门狗中。如果该看门狗由于某些原因例如 IRAM 被垃圾数据覆盖）不能执行调用 panic handler 的 NMI handler，它它将对 SoC 进行硬复位。

<<<<<<< HEAD
任务看门狗
^^^^^^^^^^^^^

任何任务都可以被任务看门狗看守。如果这个任务没有在任务看门狗所指定的超时时间（可以通过 ``make menuconfig`` 配置）内喂狗，看门狗将会打印警告消息 —— 哪些任务正在 ESP32 CPU 上面运行，哪些任务没有喂狗。

默认情况下，任务看门狗会看守空转（idle）任务。空转任务没有喂狗的原因通常是某个高优先级任务在处理循环，没有退出到低优先级任务，这可以作为有害代码（外设的 spinloop 或者陷入无线循环的任务）的指示器。

其它任务可以通过调用 ``esp_task_wdt_feed()`` 让该任务别任务看门狗看守。第一次调用这个函数时会将该任务注册到任务看门狗中；后续的调用会喂狗。如果任务不想再被看守（例如任务完成了，即将调用 ``vTaskDelete()``），则可以调用 ``esp_task_wdt_delete()``。

看门狗任务被编译到定时器组 0 的赢硬件看门狗中。如果这个看门狗由于某些原因（例如 IRAM 被垃圾数据覆盖、中断被完全禁止等）不能执行用于打印任务数据的中断 handler，它将对 SoC 进行硬复位。
=======
Task Watchdog Timer
^^^^^^^^^^^^^^^^^^^

The Task Watchdog Timer (TWDT) is responsible for detecting instances of tasks 
running for a prolonged period of time without yielding. This is a symptom of 
CPU starvation and is usually caused by a higher priority task looping without
yielding to a lower-priority task thus starving the lower priority task from
CPU time. This can be an indicator of poorly written code that spinloops on a
peripheral, or a task that is stuck in an infinite loop. 

By default the TWDT will watch the Idle Tasks of each CPU, however any task can 
elect to be watched by the TWDT. Each watched task must 'reset' the TWDT
periodically to indicate that they have been allocated CPU time. If a task does 
not reset within the TWDT timeout period, a warning will be printed with 
information about which tasks failed to reset the TWDT in time and which 
tasks are currently running on the ESP32 CPUs and.

The TWDT is built around the Hardware Watchdog Timer in Timer Group 0. The TWDT
can be initialized by calling :cpp:func:`esp_task_wdt_init` which will configure
the hardware timer. A task can then subscribe to the TWDT using 
:cpp:func:`esp_task_wdt_add` in order to be watched. Each subscribed task must 
periodically call :cpp:func:`esp_task_wdt_reset` to reset the TWDT. Failure by 
any subscribed tasks to periodically call :cpp:func:`esp_task_wdt_reset`
indicates that one or more tasks have been starved of CPU time or are stuck in a
loop somewhere.

A watched task can be unsubscribed from the TWDT using 
:cpp:func:`esp_task_wdt_delete()`. A task that has been unsubscribed should no 
longer call :cpp:func:`esp_task_wdt_reset`. Once all tasks have unsubscribed
form the TWDT, the TWDT can be deinitialized by calling 
:cpp:func:`esp_task_wdt_deinit()`.

By default :ref:`CONFIG_TASK_WDT` in ``make menuconfig`` will be enabled causing
the TWDT to be initialized automatically during startup. Likewise
:ref:`CONFIG_TASK_WDT_CHECK_IDLE_TASK_CPU0` and 
:ref:`CONFIG_TASK_WDT_CHECK_IDLE_TASK_CPU1` are also enabled by default causing
the two Idle Tasks to be subscribed to the TWDT during startup.
>>>>>>> master

JTAG 和看门狗
^^^^^^^^^^^^^^^^^^

<<<<<<< HEAD
在使用 OpenOCD 进行调试时，如果 CPU 被挂起，看门狗会继续运行，最终会复位 CPU。这导致调试代码变得非常困难；这也是为什么 OpenOCD 配置会在启动时禁止所有看门狗的原因。也就是说，当 ESP32 通过 JTAG 连接到 OpenOCD 时，你不会看到由任务看门狗或中断看门狗打印的如何警告和 panic。

API 参考手册
-------------

头文件
^^^^^^^^^^^^
=======
While debugging using OpenOCD, the CPUs will be halted every time a breakpoint 
is reached. However if the watchdog timers continue to run when a breakpoint is 
encountered, they will eventually trigger a reset making it very difficult to 
debug code. Therefore OpenOCD will disable the hardware timers of both the 
interrupt and task watchdogs at every breakpoint. Moreover, OpenOCD will not 
reenable them upon leaving the breakpoint. This means that interrupt watchdog
and task watchdog functionality will essentially be disabled. No warnings or 
panics from either watchdogs will be generated when the ESP32 is connected to 
OpenOCD via JTAG.


Interrupt Watchdog API Reference
--------------------------------

Header File
^^^^^^^^^^^
>>>>>>> master

  * :component_file:`esp32/include/esp_int_wdt.h`


函数
---------
 
.. doxygenfunction:: esp_int_wdt_init

Task Watchdog API Reference
----------------------------

A full example using the Task Watchdog is available in esp-idf: :example:`system/task_watchdog`

.. include:: /_build/inc/esp_task_wdt.inc
