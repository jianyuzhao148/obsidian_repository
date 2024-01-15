Cmake -G:指定生成器

1. **`G "Unix Makefiles"`**：生成Unix Makefiles构建系统。
2. **`G "Visual Studio 16 2019"`**：生成Visual Studio 2019构建系统。
3. **`G "Xcode"`**：生成Xcode构建系统。
4. **`G "Ninja"`**：生成Ninja构建系统。

_CMake语言不区分大小写，但是参数区分大小写_

CMakeLists.txt

# 设置CMake最低版本限制

cmake_minimum_required(VERSION 3.24 FATAL_ERROR)

# 设置项目名称和支持的编程语言

project(MyBase LANGUAGES CXX)

# 引入新变量USE_LIBRARY设置为OFF

# set(USE_LIBRARY OFF)

# 代替"set(USE_LIBRARY OFF)"另一种方式，默认为OFF，可通过CMake -D USE_LIBRARY=ON .. ，更改默认值

option(USE_LIBRARY "Compile sources into a library" OFF)

# 打印变量

message(STATUS "Compile sources into a library? ${USE_LIBRARY}")

# 设置全局变量BUILD_SHARED_LIBS为OFF，以后调用add_library省略第二个参数，将构建一个静态库

set(BUILD_SHARED_LIBS OFF)

# 引入变量_sources 包含需要编译的文件

list(APPEND _sources

DesignModel/Singleton.cpp

DesignModel/Singleton.h

DesignModel/Proxy.cpp

DesignModel/Proxy.h)

# foreach设置文件编译级别

foreach (_file ${_sources})

# 将属性设置为给定文件的传递值

set_source_files_properties(${_file} PROPERTIES COMPILE_FLAGS -O2)

message(STATUS "Appending -O2 flag for ${_file}")

# 检索给定文件所需属性的值，并将其存储在_flags变量中。

get_source_file_property(_flags ${_file} COMPILE_FLAGS)

message(STATUS "Source ${_file} has the following extra COMPILE_FLAGS: ${_flags}")

endforeach ()

# 根据USE_LIBRARY是否产生静态库

if (USE_LIBRARY)

# 创建design静态链接库

add_library(design ${_sources})

# 将main.cpp编译链接生成可执行文件MyBase

add_executable(MyBase main.cpp)

# 将design链接到可执行目标MyBase

target_link_libraries(MyBase design)

else ()

add_executable(MyBase main.cpp ${_sources})

endif ()

# 目标编译版本设置

set_target_properties(MyBase

PROPERTIES

CXX_STANDARD 11

CXX_EXTENSIONS OFF

CXX_STANDARD_REQUIRED ON)

# ==============================================Python==============================================

# 找到Python解释器

find_package(PythonInterp REQUIRED)

# 执行python 保存返回值和输出

execute_process(

COMMAND ${PYTHON_EXECUTABLE} "-c" "print('Hello,World')"

RESULT_VARIABLE _status

OUTPUT_VARIABLE _hello_world

ERROR_QUIET

OUTPUT_STRIP_TRAILING_WHITESPACE

)

message(STATUS "Python ResultCode ${_status}")

message(STATUS "Python Out ${_hello_world}")

# 找到Python库文件

find_package(PythonLibs ${PYTHON_VERSION_MAJOR}.${PYTHON_VERSION_MINOR} EXACT REQUIRED)

# 为目标设置头文件

target_include_directories(MyBase

PRIVATE

${PYTHON_INCLUDE_DIRS}

)

# 为目标链接库文件

target_link_libraries(MyBase

PRIVATE

${PYTHON_LIBRARIES}

)

# ==============================================Python===============================================

# ==============================================系统判断==============================================

if (CMAKE_SYSTEM_NAME STREQUAL "Linux")

# 设置预处理器定义 IS_LINUX、IS_WINDOWS

target_compile_definitions(MyBase PUBLIC "IS_LINUX")

message(STATUS "Linux")

elseif (CMAKE_SYSTEM_NAME STREQUAL "Windows")

target_compile_definitions(MyBase PUBLIC "IS_WINDOWS")

message(STATUS "Windows")

else ()

message(STATUS "Other")

endif ()

# ==============================================系统判断==============================================

# ==============================================编译器判断=============================================

# 设置预处理器定义 IS_INTEL_CXX_COMPILER、IS_GNU_CXX_COMPILER、IS_PGI_CXX_COMPILER、IS_XL_CXX_COMPILER

target_compile_definitions(MyBase PUBLIC "COMPILER_NAME=\"${CMAKE_CXX_COMPILER_ID}\"")

if (CMAKE_CXX_COMPILER_ID MATCHES Intel)

target_compile_definitions(MyBase PUBLIC "IS_INTEL_CXX_COMPILER")

message(STATUS "Intel")

endif ()

if (CMAKE_CXX_COMPILER_ID MATCHES GNU)

target_compile_definitions(MyBase PUBLIC "IS_GNU_CXX_COMPILER")

message(STATUS "GNU")

endif ()

if (CMAKE_CXX_COMPILER_ID MATCHES PGI)

target_compile_definitions(MyBase PUBLIC "IS_PGI_CXX_COMPILER")

message(STATUS "PGI")

endif ()

if (CMAKE_CXX_COMPILER_ID MATCHES XL)

target_compile_definitions(MyBase PUBLIC "IS_XL_CXX_COMPILER")

message(STATUS "XL")

endif ()

# ==============================================编译器判断=============================================

# ==============================================64/32判断==============================================

# 系统是32 or 64

# 设置预处理器定义Is_64_BIT、Is_32_BIT

if (CMAKE_SIZEOF_VOID_P EQUAL 8)

target_compile_definitions(MyBase PUBLIC "Is_64_BIT")

message(STATUS "64 BIT")

else ()

target_compile_definitions(MyBase PUBLIC "Is_32_BIT")

message(STATUS "32 BIT")

endif ()

# ==============================================64/32判断==============================================

# ==============================================系统架构判断==============================================

if (CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "i386")

message(STATUS "i386")

elseif (CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "i686")

message(STATUS "i686")

elseif (CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "x86_64")

message(STATUS "x86_64")

else ()

message(STATUS "unknown")

endif ()

# 设置预处理器定义ARCHITECTURE=${CMAKE_HOST_SYSTEM_PROCESSOR}

target_compile_definitions(MyBase

PUBLIC "ARCHITECTURE=${CMAKE_HOST_SYSTEM_PROCESSOR}")

# ==============================================系统架构判断==============================================

1. 指定生成器配置项目：CMake -G <生成器> ..
2. add_library 第二个参数的有效值<设置全局变量BUILD_SHARED_LIBS后可省略>：
    - STATIC：用于创建静态库，即编译文件的打包存档，以便在链接其他目标时使用，例如：可执行文件。
    - SHARED：用于创建动态库，即可以动态链接，并在运行时加载的库。可以在CMakeLists.txt中使用add_library(message SHARED Message.hpp Message.cpp) 从静态库切换到动态共享对象(DSO)。
    - OBJECT：可将给定add_library的列表中的源码编译到目标文件，不将它们归档到静态库中，也不能将它们链接到共享对象中。如果需要一次性创建静态库和动态库，那么使用对象库尤其有用。我们将在本示例中演示。
    - MODULE：又为DSO组。与SHARED库不同，它们不链接到项目中的任何目标，不过可以进行动态加载。该参数可以用于构建运行时插件。
3. message 第一个参数有效值：
    - (无) = 重要消息；
    - STATUS = 非重要消息；
    - WARNING = CMake 警告, 会继续执行；
    - AUTHOR_WARNING = CMake 警告 (dev), 会继续执行；
    - SEND_ERROR = CMake 错误, 继续执行，但是会跳过生成的步骤；
    - FATAL_ERROR = CMake 错误, 终止所有处理过程；
4. 局部变量应当以下划线"_"开头
5. 关于逻辑值：
    - 如果将逻辑变量设置为以下任意一种：1、ON、YES、true、Y或非零数，则逻辑变量为true。
    - 如果将逻辑变量设置为以下任意一种：0、OFF、NO、false、N、IGNORE、NOTFOUND、空字符串，或者以-NOTFOUND为后缀，则逻辑变量为false。
6. 通过-D选项指定编译器：CMake将语言的编译器存储在 CMAKE_<LANG>_COMPILER变量中，其中 <LANG>是受支持的任何一种语言，例如：CXX、C或Fortran。cmake -D CMAKE_CXX_COMPILER=clang++ ..
7. 通过-D选项指定构建类型：cmake -D CMAKE_BUILD_TYPE=Debug ..
    - Debug：用于在没有优化的情况下，使用带有调试符号构建库或可执行文件。
    - Release：用于构建的优化的库或可执行文件，不包含调试符号。
    - RelWithDebInfo：用于构建较少的优化库或可执行文件，包含调试符号。
    - MinSizeRel：用于不增加目标代码大小的优化方式，来构建库或可执行文件。
8. 为目标设置编译选项：target_compile_options(compute-areas PRIVATE "-fPIC")

第一个参数可以是库、可执行文件

第二个参数的三个可见级别：

- PRIVATE，编译选项会应用于给定的目标，不会传递给与目标相关的目标。我们的示例中， 即使compute-areas将链接到geometry库，compute-areas也不会继承geometry目标上设置的编译器选项。
- INTERFACE，给定的编译选项将只应用于指定目标，并传递给与目标相关的目标。
- PUBLIC，编译选项将应用于指定目标和使用它的目标。

1. 为目标设置属性：set_target_properties(MyBase PROPERTIES CXX_STANDARD 11 CXX_EXTENSIONS OFF CXX_STANDARD_REQUIRED ON)
    - CXX_STANDARD会设置我们想要的标准。
    - CXX_EXTENSIONS告诉CMake，只启用ISO C++标准的编译器标志，而不使用特定编译器的扩展。
    - CXX_STANDARD_REQUIRED指定所选标准的版本。如果这个版本不可用，CMake将停止配置并出现错误。当这个属性被设置为OFF时，CMake将寻找下一个标准的最新版本，直到一个合适的标志。这意味着，首先查找C++14，然后是C++11，然后是C++98。（译者注：目前会从C++20或C++17开始查找）
2. 为目标设置预处理器定义：target_compile_definitions(MyBase PUBLIC "IS_LINUX")

第一个参数为库、可执行文件

第二个参数可选：

- PRIVATE，编译定义将只应用于给定的目标，而不应用于相关的其他目标。
- INTERFACE，对给定目标的编译定义将只应用于使用它的目标。
- PUBLIC，编译定义将应用于给定的目标和使用它的所有其他目标。

总结：

1. 两条指令<link_libraries和target_link_directories>效果一致，但带了 target命令的可以指定目标<库or可执行文件> 和 可见级别<PRIVATE,INTERFACE,PUBLIC>
2. target_link_directories 和 target_link_libraries 分别对应g++的-L 和-l，接受多个参数空格分割