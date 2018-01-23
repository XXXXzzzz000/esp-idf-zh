构建系统
************

本节阐释了乐鑫 IoT 开发框架的编译系统以及 “组件（components）” 的概念。

如果你想知道如何组织已给新的 ESP-IDF 工程的话，请继续阅读本文档。

我们推荐你使用 esp-idf-template_ 工程作为你的工程的起点。

使用构建系统
======================

esp-idf 的 README 文件包含了如何使用构建系统构建你自己的工程的描述。

概述
========

ESP-IDF 工程可以看做大量组件构成的集合。例如，一个显示当前空气湿度的 webserver 可能包括：

- ESP32 基本库（libc，rom bindings 等）
- WiFi drivers
- TCP/IP 协议栈
- FreeRTOS 操作系统
- webserver
- 湿度传感器
- 将它们集成到一个的主代码

ESP-IDF 中组件的概念非常清晰明确，且是可配置的。当编译工程时，构建环境将查找 ESP-IDF 目录下面的所有组件、工程目录以及额外自定义的目录（可选）。它允许用户通过一个基于文本
的菜单系统对 ESP-IDF 工程进行配置，以自定义每个组件。当工程中的组件被配置后，构建过程将会编译工程。

概念
--------

- "工程（project）" 是一个目录，它包含需要编译成 “app”（以及一些额外支持的输出，例如分区表，数据/文件系统分区，bootloader）的所有文件和配置。

- "工程配置（Project configuration）" 是一个位于工程根目录的叫做 sdkconfig 的文件。这个配置文件可以通过 ``make menuconfig`` 来自定义工程的配置项。单个工程确切地包含一个工程配置。

- "应用程序（app）" 是一个由 esp-idf 编译成的可执行文件。单个工程通常会被编译成两个 app - 一个 "工程 app" （主可执行文件，即你的自定义固件）和一个 "bootloader app"（加载工程 app 的初始化 bootloader 程序）。

- "组件（component）" 是由相对独立的代码构成的模块，它们会被编译乘静态库（.a 文件）并被链接到 app 中。某些组件是由 esp-idf 自身提供的，另外一些组件可能在其它地方。

下面这些东西不属于工程的内容：

- "ESP-IDF" 不是工程的一部分，而是一个相对独立的代码，通过环境变量 ``IDF_PATH`` 链接到工程。这能减小 IED 框架与工程直接的耦合性。

- 用于编译的工具链不是工程的一部分。工具链应当安装在系统命令行的 PATH 中，或者工程配置中所配置的工具链前缀所对应的路径中。

示例工程
---------------

一个示例工程目录大概如下 ::

    - myProject/
                 - Makefile
                 - sdkconfig
                 - components/ - component1/ - component.mk
                                             - Kconfig
                                             - src1.c
                               - component2/ - component.mk
                                             - Kconfig
                                             - src1.c
                                             - include/ - component2.h
                 - main/       - src1.c
                               - src2.c
                               - component.mk

                 - build/

这个示例 "myProject" 包含如下元素：

- 一个顶层工程 Makefile。这个 Makefile 设置变量 ``PROJECT_NAME``，并（可选）定义项目范围内的其它 make 变量。 它包含了核心 makefile ``$(IDF_PATH)/make/project.mk``，这个核心 makefile 实现了 ESP-IDF 构建系统的余下部分。

- "sdkconfig" 工程配置文件。这个文件会在运行 "make menuconfig" 时被创建/更新，它包含了工程中的所有组件（包括 esp-idf 自身）。"sdkconfig" 可以被添加到工程的源码控制系统中，也可以不添加到工程的源码控制系统中。

- 可选的 "组件（components）" 目录包含了一些组件，这些组件属于工程的一部分。工程并非必须包含这种自定义的组件，但是这通常有利于构架可充用的代码或者包含不属于 ESP-IDF 的第三方组件。

<<<<<<< HEAD
- "main" 目录是一个特殊的 "虚拟组件"，它包含工程自身的源代码。"main" 是一个特殊的名字，Makefile 变量 ``SRCDIRS`` 默认就是这个值。用于也可以通过设置该变量让构建系统在其它目录来查找这个虚拟组件。
=======
- "main" directory is a special "pseudo-component" that contains source code for the project itself. "main" is a default name, the Makefile variable ``COMPONENT_DIRS`` includes this component but you can modify this variable (or set ``EXTRA_COMPONENT_DIRS``) to look for components in other places.
>>>>>>> master

- "build" 是构架系统所创建的构建输出目录。运行 make 命令后，这个变量将包含临时目标文件、库文件和最中国的二进制输出文件。该目录通常不会添加到源代码控制系统中，或者不会随着工程源代码一起发布。

<<<<<<< HEAD
组件目录包含一个组件 makefile - ``component.mk``。它里面可能包含用于控制组件编译过程的变量定义，被集成到整个工程。更多细节请参考 `Component Makefiles`。
=======
Component directories contain a component makefile - ``component.mk``. This may contain variable definitions
to control the build process of the component, and its integration into the overall project. See `Component Makefiles`_ for more details.
>>>>>>> master

每个组件可以包含一个 ``Kconfig`` 文件，用于定义可以通过工程配置进行设置的 `组件配置` 选项。
一些组件可能还包括 ``Kconfig.projbuild`` 和 ``Makefile.projbuild`` —— 用于 `覆盖构成的某一部分` 的特殊文件。

工程 Makefile
-----------------

每个工程都有一个包含整个工程构建设置的 Makefile。默认情况下，工程的 Makefile 非常简单。

最小的 Makefile 示例
^^^^^^^^^^^^^^^^^^^^^^^^

::

   PROJECT_NAME := myProject
   
   include $(IDF_PATH)/make/project.mk


强制的工程变量
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- ``PROJECT_NAME``: 工程的名字。二进制输出文件会使用这个名字 - 即 myProject.bin, myProject.elf。

可选的工程变量
^^^^^^^^^^^^^^^^^^^^^^^^^^

下面的这些变量都有一个默认值，但是你可以覆盖它们来实现一些自定义的行为。具体的实现细节请查看 ``make/project.mk``。

- ``PROJECT_PATH``: Top-level project directory. Defaults to the directory containing the Makefile. Many other project variables are based on this variable. The project path cannot contain spaces.
- ``BUILD_DIR_BASE``: The build directory for all objects/libraries/binaries. Defaults to ``$(PROJECT_PATH)/build``.
- ``COMPONENT_DIRS``: Directories to search for components. Defaults to `$(IDF_PATH)/components`, `$(PROJECT_PATH)/components`, ``$(PROJECT_PATH)/main`` and ``EXTRA_COMPONENT_DIRS``. Override this variable if you don't want to search for components in these places.
- ``EXTRA_COMPONENT_DIRS``: Optional list of additional directories to search for components.
- ``COMPONENTS``: A list of component names to build into the project. Defaults to all components found in the COMPONENT_DIRS directories.

Any paths in these Makefile variables should be absolute paths. You can convert relative paths using ``$(PROJECT_PATH)/xxx``, ``$(IDF_PATH)/xxx``, or use the Make function ``$(abspath xxx)``.

These variables should all be set before the line ``include $(IDF_PATH)/make/project.mk`` in the Makefile.

组件的 Makefile
-------------------

Each project contains one or more components, which can either be part of esp-idf or added from other component directories.

A component is any directory that contains a ``component.mk`` file.

Searching for Components
------------------------

The list of directories in ``COMPONENT_DIRS`` is searched for the project's components. Directories in this list can either be components themselves (ie they contain a `component.mk` file), or they can be top-level directories whose subdirectories are components.

Running the ``make list-components`` target dumps many of these variables and can help debug the discovery of component directories.

Multiple components with the same name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When esp-idf is collecting all the components to compile, it will do this in the order specified by ``COMPONENT_DIRS``; by default, this means the 
idf components first, the project components second and optionally the components in ``EXTRA_COMPONENT_DIRS`` last. If two or more of these directories
contain component subdirectories with the same name, the component in the last place searched is used. This allows, for example, overriding esp-idf components 
with a modified version by simply copying the component from the esp-idf component directory to the project component tree and then modifying it there. 
If used in this way, the esp-idf directory itself can remain untouched.

Minimal Component Makefile
^^^^^^^^^^^^^^^^^^^^^^^^^^

The minimal ``component.mk`` file is an empty file(!). If the file is empty, the default component behaviour is set:

- All source files in the same directory as the makefile (``*.c``, ``*.cpp``, ``*.cc``, ``*.S``) will be compiled into the component library
- A sub-directory "include" will be added to the global include search path for all other components.
- The component library will be linked into the project app.

See `example component makefiles`_ for more complete component makefile examples.

Note that there is a difference between an empty ``component.mk`` file (which invokes default component build behaviour) and no ``component.mk`` file (which means no default component build behaviour will occur.) It is possible for a component to have no `component.mk` file, if it only contains other files which influence the project configuration or build process.

.. component variables:

Preset Component Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^

The following component-specific variables are available for use inside ``component.mk``, but should not be modified:

- ``COMPONENT_PATH``: The component directory. Evaluates to the absolute path of the directory containing ``component.mk``. The component path cannot contain spaces.
- ``COMPONENT_NAME``: Name of the component. Defaults to the name of the component directory.
- ``COMPONENT_BUILD_DIR``: The component build directory. Evaluates to the absolute path of a directory inside `$(BUILD_DIR_BASE)` where this component's source files are to be built. This is also the Current Working Directory any time the component is being built, so relative paths in make targets, etc. will be relative to this directory.
- ``COMPONENT_LIBRARY``: Name of the static library file (relative to the component build directory) that will be built for this component. Defaults to ``$(COMPONENT_NAME).a``.

The following variables are set at the project level, but exported for use in the component build:

- ``PROJECT_NAME``: Name of the project, as set in project Makefile
- ``PROJECT_PATH``: Absolute path of the project directory containing the project Makefile.
- ``COMPONENTS``: Name of all components that are included in this build.
- ``CONFIG_*``: Each value in the project configuration has a corresponding variable available in make. All names begin with ``CONFIG_``.
- ``CC``, ``LD``, ``AR``, ``OBJCOPY``: Full paths to each tool from the gcc xtensa cross-toolchain.
- ``HOSTCC``, ``HOSTLD``, ``HOSTAR``: Full names of each tool from the host native toolchain.
- ``IDF_VER``: Git version of ESP-IDF (produced by ``git describe``)

If you modify any of these variables inside ``component.mk`` then this will not prevent other components from building but it may make your component hard to build and/or debug.

Optional Project-Wide Component Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following variables can be set inside ``component.mk`` to control build settings across the entire project:

- ``COMPONENT_ADD_INCLUDEDIRS``: Paths, relative to the component
  directory, which will be added to the include search path for
  all components in the project. Defaults to ``include`` if not overridden. If an include directory is only needed to compile
  this specific component, add it to ``COMPONENT_PRIV_INCLUDEDIRS`` instead.
- ``COMPONENT_ADD_LDFLAGS``: Add linker arguments to the LDFLAGS for
  the app executable. Defaults to ``-l$(COMPONENT_NAME)``.  If
  adding pre-compiled libraries to this directory, add them as
  absolute paths - ie $(COMPONENT_PATH)/libwhatever.a
- ``COMPONENT_DEPENDS``: Optional list of component names that should
  be compiled before this component. This is not necessary for
  link-time dependencies, because all component include directories
  are available at all times. It is necessary if one component
  generates an include file which you then want to include in another
  component. Most components do not need to set this variable.
- ``COMPONENT_ADD_LINKER_DEPS``: Optional list of component-relative paths
  to files which should trigger a re-link of the ELF file if they change.
  Typically used for linker script files and binary libraries. Most components do
  not need to set this variable.

The following variable only works for components that are part of esp-idf itself:

- ``COMPONENT_SUBMODULES``: Optional list of git submodule paths
  (relative to COMPONENT_PATH) used by the component. These will be
  checked (and initialised if necessary) by the build process. This
  variable is ignored if the component is outside the IDF_PATH
  directory.


Optional Component-Specific Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following variables can be set inside ``component.mk`` to control the build of that component:

- ``COMPONENT_PRIV_INCLUDEDIRS``: Directory paths, must be relative to
  the component directory, which will be added to the include search
  path for this component's source files only.
- ``COMPONENT_EXTRA_INCLUDES``: Any extra include paths used when
  compiling the component's source files. These will be prefixed with
  '-I' and passed as-is to the compiler.  Similar to the
  ``COMPONENT_PRIV_INCLUDEDIRS`` variable, except these paths are not
  expanded relative to the component directory.
- ``COMPONENT_SRCDIRS``: Directory paths, must be relative to the
  component directory, which will be searched for source files (``*.cpp``,
  ``*.c``, ``*.S``). Defaults to '.', ie the component directory
  itself. Override this to specify a different list of directories
  which contain source files.
- ``COMPONENT_OBJS``: Object files to compile. Default value is a .o
  file for each source file that is found in ``COMPONENT_SRCDIRS``.
  Overriding this list allows you to exclude source files in
  ``COMPONENT_SRCDIRS`` that would otherwise be compiled. See
  `Specifying source files`
- ``COMPONENT_EXTRA_CLEAN``: Paths, relative to the component build
  directory, of any files that are generated using custom make rules
  in the component.mk file and which need to be removed as part of
  ``make clean``. See `Source Code Generation`_ for an example.
- ``COMPONENT_OWNBUILDTARGET`` & ``COMPONENT_OWNCLEANTARGET``: These
  targets allow you to fully override the default build behaviour for
  the component. See `Fully Overriding The Component Makefile`_ for
  more details.
- ``COMPONENT_CONFIG_ONLY``: If set, this flag indicates that the component
  produces no built output at all (ie ``COMPONENT_LIBRARY`` is not built),
  and most other component variables are ignored. This flag is used for IDF
  internal components which contain only ``KConfig.projbuild`` and/or
  ``Makefile.projbuild`` files to configure the project, but no source files.
- ``CFLAGS``: Flags passed to the C compiler. A default set of
  ``CFLAGS`` is defined based on project settings. Component-specific
  additions can be made via ``CFLAGS +=``. It is also possible
  (although not recommended) to override this variable completely for
  a component.
- ``CPPFLAGS``: Flags passed to the C preprocessor (used for .c, .cpp
  and .S files). A default set of ``CPPFLAGS`` is defined based on
  project settings. Component-specific additions can be made via
  ``CPPFLAGS +=``. It is also possible (although not recommended) to
  override this variable completely for a component.
- ``CXXFLAGS``: Flags passed to the C++ compiler. A default set of
  ``CXXFLAGS`` is defined based on project
  settings. Component-specific additions can be made via ``CXXFLAGS
  +=``. It is also possible (although not recommended) to override
  this variable completely for a component.

To apply compilation flags to a single source file, you can add a variable override as a target, ie::

  apps/dhcpserver.o: CFLAGS += -Wno-unused-variable

This can be useful if there is upstream code that emits warnings.

Component Configuration
-----------------------

Each component can also have a Kconfig file, alongside ``component.mk``. This contains contains
configuration settings to add to the "make menuconfig" for this component.

These settings are found under the "Component Settings" menu when menuconfig is run.

To create a component KConfig file, it is easiest to start with one of the KConfig files distributed with esp-idf.

For an example, see `Adding conditional configuration`_.

Preprocessor Definitions
------------------------

ESP-IDF build systems adds the following C preprocessor definitions on the command line:

- ``ESP_PLATFORM`` — Can be used to detect that build happens within ESP-IDF.
- ``IDF_VER`` — Defined to a git version string.  E.g. ``v2.0`` for a tagged release or ``v1.0-275-g0efaa4f`` for an arbitrary commit.

Build Process Internals
-----------------------

Top Level: Project Makefile
^^^^^^^^^^^^^^^^^^^^^^^^^^^

- "make" is always run from the project directory and the project makefile, typically named Makefile.
- The project makefile sets ``PROJECT_NAME`` and optionally customises other `optional project variables`
- The project makefile includes ``$(IDF_PATH)/make/project.mk`` which contains the project-level Make logic.
- ``project.mk`` fills in default project-level make variables and includes make variables from the project configuration. If the generated makefile containing project configuration is out of date, then it is regenerated (via targets in ``project_config.mk``) and then the make process restarts from the top.
- ``project.mk`` builds a list of components to build, based on the default component directories or a custom list of components set in `optional project variables`.
- Each component can set some `optional project-wide component variables`_. These are included via generated makefiles named ``component_project_vars.mk`` - there is one per component. These generated makefiles are included into ``project.mk``. If any are missing or out of date, they are regenerated (via a recursive make call to the component makefile) and then the make process restarts from the top.
- `Makefile.projbuild` files from components are included into the make process, to add extra targets or configuration. 
- By default, the project makefile also generates top-level build & clean targets for each component and sets up `app` and `clean` targets to invoke all of these sub-targets.
- In order to compile each component, a recursive make is performed for the component makefile.

To better understand the project make process, have a read through the ``project.mk`` file itself.

Second Level: Component Makefiles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Each call to a component makefile goes via the ``$(IDF_PATH)/make/component_wrapper.mk`` wrapper makefile.
- This component wrapper includes all component ``Makefile.componentbuild`` files, making any recipes, variables etc in these files available to every component.
- The ``component_wrapper.mk`` is called with the current directory set to the component build directory, and the ``COMPONENT_MAKEFILE`` variable is set to the absolute path to ``component.mk``.
- ``component_wrapper.mk`` sets default values for all `component variables`, then includes the `component.mk` file which can override or modify these.
- If ``COMPONENT_OWNBUILDTARGET`` and ``COMPONENT_OWNCLEANTARGET`` are not defined, default build and clean targets are created for the component's source files and the prerequisite ``COMPONENT_LIBRARY`` static library file.
- The ``component_project_vars.mk`` file has its own target in ``component_wrapper.mk``, which is evaluated from ``project.mk`` if this file needs to be rebuilt due to changes in the component makefile or the project configuration.

To better understand the component make process, have a read through the ``component_wrapper.mk`` file and some of the ``component.mk`` files included with esp-idf.

Running Make Non-Interactively
------------------------------

When running ``make`` in a situation where you don't want interactive prompts (for example: inside an IDE or an automated build system) append ``BATCH_BUILD=1`` to the make arguments (or set it as an environment variable).

Setting ``BATCH_BUILD`` implies the following:

- Verbose output (same as ``V=1``, see below). If you don't want verbose output, also set ``V=0``.
- If the project configuration is missing new configuration items (from new components or esp-idf updates) then the project use the default values, instead of prompting the user for each item.
- If the build system needs to invoke ``menuconfig``, an error is printed and the build fails.

Debugging The Make Process
--------------------------

Some tips for debugging the esp-idf build system:

- Appending ``V=1`` to the make arguments (or setting it as an environment variable) will cause make to echo all commands executed, and also each directory as it is entered for a sub-make.
- Running ``make -w`` will cause make to echo each directory as it is entered for a sub-make - same as ``V=1`` but without also echoing all commands.
- Running ``make --trace`` (possibly in addition to one of the above arguments) will print out every target as it is built, and the dependency which caused it to be built.
- Running ``make -p`` prints a (very verbose) summary of every generated target in each makefile.

For more debugging tips and general make information, see the `GNU Make Manual`.

.. _warn-undefined-variables:

Warning On Undefined Variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default, the build process will print a warning if an undefined variable is referenced (like ``$(DOES_NOT_EXIST)``). This can be useful to find errors in variable names.

If you don't want this behaviour, it can be disabled in menuconfig's top level menu under `SDK tool configuration`.

Note that this option doesn't trigger a warning if ``ifdef`` or ``ifndef`` are used in Makefiles.

Overriding Parts of the Project
-------------------------------

Makefile.projbuild
^^^^^^^^^^^^^^^^^^

For components that have build requirements that must be evaluated in the top-level
project make pass, you can create a file called ``Makefile.projbuild`` in the
component directory. This makefile is included when ``project.mk`` is evaluated.

For example, if your component needs to add to CFLAGS for the entire
project (not just for its own source files) then you can set
``CFLAGS +=`` in Makefile.projbuild.

``Makefile.projbuild`` files are used heavily inside esp-idf, for defining project-wide build features such as ``esptool.py`` command line arguments and the ``bootloader`` "special app".

Note that ``Makefile.projbuild`` isn't necessary for the most common component uses - such as adding include directories to the project, or LDFLAGS to the final linking step. These values can be customised via the ``component.mk`` file itself. See `Optional Project-Wide Component Variables`_ for details.

Take care when setting variables or targets in this file. As the values are included into the top-level project makefile pass, they can influence or break functionality across all components!

KConfig.projbuild
^^^^^^^^^^^^^^^^^

This is an equivalent to ``Makefile.projbuild`` for `component configuration` KConfig files. If you want to include
configuration options at the top-level of menuconfig, rather than inside the "Component Configuration" sub-menu, then these can be defined in the KConfig.projbuild file alongside the ``component.mk`` file.

Take care when adding configuration values in this file, as they will be included across the entire project configuration. Where possible, it's generally better to create a KConfig file for `component configuration`.


Makefile.componentbuild
^^^^^^^^^^^^^^^^^^^^^^^

For components that e.g. include tools to generate source files from other files, it is necessary to be able to add recipes, macros or variable definitions
into the component build process of every components. This is done by having a ``Makefile.componentbuild`` in a component directory. This file gets included
in ``component_wrapper.mk``, before the ``component.mk`` of the component is included. As with the Makefile.projbuild, take care with these files: as they're
included in each component build, a ``Makefile.componentbuild`` error may only show up when compiling an entirely different component.

Configuration-Only Components
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Some special components which contain no source files, only ``Kconfig.projbuild`` and ``Makefile.projbuild``, may set the flag ``COMPONENT_CONFIG_ONLY`` in the component.mk file. If this flag is set, most other component variables are ignored and no build step is run for the component.

Example Component Makefiles
---------------------------

Because the build environment tries to set reasonable defaults that will work most
of the time, component.mk can be very small or even empty (see `Minimal Component Makefile`_). However, overriding `component variables` is usually required for some functionality.

Here are some more advanced examples of ``component.mk`` makefiles:


Adding source directories
^^^^^^^^^^^^^^^^^^^^^^^^^

By default, sub-directories are ignored. If your project has sources in sub-directories
instead of in the root of the component then you can tell that to the build
system by setting ``COMPONENT_SRCDIRS``::

    COMPONENT_SRCDIRS := src1 src2

This will compile all source files in the src1/ and src2/ sub-directories
instead.

Specifying source files
^^^^^^^^^^^^^^^^^^^^^^^

The standard component.mk logic adds all .S and .c files in the source
directories as sources to be compiled unconditionally. It is possible
to circumvent that logic and hard-code the objects to be compiled by
manually setting the ``COMPONENT_OBJS`` variable to the name of the
objects that need to be generated::

    COMPONENT_OBJS := file1.o file2.o thing/filea.o thing/fileb.o anotherthing/main.o
    COMPONENT_SRCDIRS := . thing anotherthing

Note that ``COMPONENT_SRCDIRS`` must be set as well.

Adding conditional configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The configuration system can be used to conditionally compile some files
depending on the options selected in ``make menuconfig``. For this, ESP-IDF
has the compile_only_if and compile_only_if_not macros:

``Kconfig``::

    config FOO_ENABLE_BAR
        bool "Enable the BAR feature."
        help
            This enables the BAR feature of the FOO component.

``component.mk``::

    $(call compile_only_if,$(CONFIG_FOO_ENABLE_BAR),bar.o)


As can be seen in the example, the ``compile_only_if`` macro takes a condition and a 
list of object files as parameters. If the condition is true (in this case: if the
BAR feature is enabled in menuconfig) the object files (in this case: bar.o) will
always be compiled. The opposite goes as well: if the condition is not true, bar.o 
will never be compiled. ``compile_only_if_not`` does the opposite: compile if the 
condition is false, not compile if the condition is true.

This can also be used to select or stub out an implementation, as such:

``Kconfig``::

    config ENABLE_LCD_OUTPUT
        bool "Enable LCD output."
        help
            Select this if your board has a LCD.

    config ENABLE_LCD_CONSOLE
        bool "Output console text to LCD"
        depends on ENABLE_LCD_OUTPUT
        help
            Select this to output debugging output to the lcd

    config ENABLE_LCD_PLOT
        bool "Output temperature plots to LCD"
        depends on ENABLE_LCD_OUTPUT
        help
            Select this to output temperature plots


``component.mk``::

    # If LCD is enabled, compile interface to it, otherwise compile dummy interface
    $(call compile_only_if,$(CONFIG_ENABLE_LCD_OUTPUT),lcd-real.o lcd-spi.o)
    $(call compile_only_if_not,$(CONFIG_ENABLE_LCD_OUTPUT),lcd-dummy.o)

    #We need font if either console or plot is enabled
    $(call compile_only_if,$(or $(CONFIG_ENABLE_LCD_CONSOLE),$(CONFIG_ENABLE_LCD_PLOT)), font.o)

Note the use of the Make 'or' function to include the font file. Other substitution functions,
like 'and' and 'if' will also work here. Variables that do not come from menuconfig can also 
be used: ESP-IDF uses the default Make policy of judging a variable which is empty or contains 
only whitespace to be false while a variable with any non-whitespace in it is true.

(Note: Older versions of this document advised conditionally adding object file names to
``COMPONENT_OBJS``. While this still is possible, this will only work when all object 
files for a component are named explicitely, and will not clean up deselected object files
in a ``make clean`` pass.)

Source Code Generation
^^^^^^^^^^^^^^^^^^^^^^

Some components will have a situation where a source file isn't
supplied with the component itself but has to be generated from
another file. Say our component has a header file that consists of the
converted binary data of a BMP file, converted using a hypothetical
tool called bmp2h. The header file is then included in as C source
file called graphics_lib.c::

    COMPONENT_EXTRA_CLEAN := logo.h

    graphics_lib.o: logo.h

    logo.h: $(COMPONENT_PATH)/logo.bmp
        bmp2h -i $^ -o $@


In this example, graphics_lib.o and logo.h will be generated in the
current directory (the build directory) while logo.bmp comes with the
component and resides under the component path. Because logo.h is a
generated file, it needs to be cleaned when make clean is called which
why it is added to the COMPONENT_EXTRA_CLEAN variable.

Cosmetic Improvements
^^^^^^^^^^^^^^^^^^^^^

Because logo.h is a generated file, it needs to be cleaned when make
clean is called which why it is added to the COMPONENT_EXTRA_CLEAN
variable.

Adding logo.h to the ``graphics_lib.o`` dependencies causes it to be
generated before ``graphics_lib.c`` is compiled.

If a a source file in another component included ``logo.h``, then this
component's name would have to be added to the other component's
``COMPONENT_DEPENDS`` list to ensure that the components were built
in-order.

Embedding Binary Data
^^^^^^^^^^^^^^^^^^^^^

Sometimes you have a file with some binary or text data that you'd like to make available to your component - but you don't want to reformat the file as C source.

You can set a variable COMPONENT_EMBED_FILES in component.mk, giving the names of the files to embed in this way::

  COMPONENT_EMBED_FILES := server_root_cert.der

Or if the file is a string, you can use the variable COMPONENT_EMBED_TXTFILES. This will embed the contents of the text file as a null-terminated string::

  COMPONENT_EMBED_TXTFILES := server_root_cert.pem

The file's contents will be added to the .rodata section in flash, and are available via symbol names as follows::

  extern const uint8_t server_root_cert_pem_start[] asm("_binary_server_root_cert_pem_start");
  extern const uint8_t server_root_cert_pem_end[]   asm("_binary_server_root_cert_pem_end");

The names are generated from the full name of the file, as given in COMPONENT_EMBED_FILES. Characters /, ., etc. are replaced with underscores. The _binary prefix in the symbol name is added by objcopy and is the same for both text and binary files.

For an example of using this technique, see :example:`protocols/https_request` - the certificate file contents are loaded from the text .pem file at compile time.


Fully Overriding The Component Makefile
---------------------------------------

Obviously, there are cases where all these recipes are insufficient for a
certain component, for example when the component is basically a wrapper
around another third-party component not originally intended to be
compiled under this build system. In that case, it's possible to forego
the esp-idf build system entirely by setting COMPONENT_OWNBUILDTARGET and
possibly  COMPONENT_OWNCLEANTARGET and defining your own targets named ``build`` and ``clean`` in ``component.mk``
target. The build target can do anything as long as it creates
$(COMPONENT_LIBRARY) for the project make process to link into the app binary.

(Actually, even this is not strictly necessary - if the COMPONENT_ADD_LDFLAGS variable
is overridden then the component can instruct the linker to link other binaries instead.)


.. _esp-idf-template: https://github.com/espressif/esp-idf-template
.. _GNU Make Manual: https://www.gnu.org/software/make/manual/make.html


.. _custom-sdkconfig-defaults:

Custom sdkconfig defaults
-------------------------

For example projects or other projects where you don't want to specify a full sdkconfig configuration, but you do want to override some key values from the esp-idf defaults, it is possible to create a file ``sdkconfig.defaults`` in the project directory. This file will be used when running ``make defconfig``, or creating a new config from scratch.

To override the name of this file, set the ``SDKCONFIG_DEFAULTS`` environment variable.


Save flash arguments
--------------------

There're some scenarios that we want to flash the target board without IDF. For this case we want to save the built binaries, esptool.py and esptool write_flash arguments. It's simple to write a script to save binaries and esptool.py. We can use command ``make print_flash_cmd``, it will print the flash arguments::

    --flash_mode dio --flash_freq 40m --flash_size detect 0x1000 bootloader/bootloader.bin 0x10000 example_app.bin 0x8000 partition_table_unit_test_app.bin

Then use flash arguments as the arguemnts for esptool write_flash arguments::

    python esptool.py --chip esp32 --port /dev/ttyUSB0 --baud 921600 --before default_reset --after hard_reset write_flash -z --flash_mode dio --flash_freq 40m --flash_size detect 0x1000 bootloader/bootloader.bin 0x10000 example_app.bin 0x8000 partition_table_unit_test_app.bin

Building the Bootloader
=======================

The bootloader is built by default as part of "make all", or can be built standalone via "make bootloader-clean". There is also "make bootloader-list-components" to see the components included in the bootloader build.

The component in IDF components/bootloader is special, as the second stage bootloader is a separate .ELF and .BIN file to the main project. However it shares its configuration and build directory with the main project.

This is accomplished by adding a subproject under components/bootloader/subproject. This subproject has its own Makefile, but it expects to be called from the project's own Makefile via some glue in the components/bootloader/Makefile.projectbuild file. See these files for more details.
