# CMake

## Build system

* Using CMakeLists.txt file for each target
* platform: -G 'MSYS Makefiles'
* build type: 
  * -DCMAKE\_BUILD\_TYPE=Debug
  * SET\( CMAKE\_BUILD\_TYPE Release ... FORCE \)

```text
$ mkdir Debug
$ cd Debug
$ cmake -G "MSYS Makefiles" -DCMAKE_BUILD_TYPE=Debug
$ make VERBOSE=1
```

## Basic structure

### version require and project name

```text
cmake_minimum_required(VERSION 3.2)
project(testPilot)
```

### set gcc and g++

There are three methods to set gcc and g++ for cmake

#### use environment variables

This method the value will read from CMake cache, the export will need to do once.

```text
export CC=/usr/local/bin/gcc
export CXX=/usr/local/bin/g++
cmake /path/to/your/project
make
```

#### Without cache

_these commands need to in frontof project_ This time you create a "normal" variable "CMAKE\_C\(XX\)\_COMPILER". If you have multiple platform to build, you need to change CMakeLists every time.

```text
set(CMAKE_C_COMPILER /usr/bin/clang CACHE PATH "")
set(CMAKE_CXX_COMPILER /usr/bin/clang++ CACHE PATH "")
```

### set output directory

* global

  ```text
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
  ```

* target

  ```text
  set_target_properties(dspfpy PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/test"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/test"
  RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/test"
  )
  ```

### add compiler and linker flags

```text
set (CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")
set(GXX_COMPILE_FLAGS "-Wall")
set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} ${GXX_COMPILE_FLAGS}" )
set(GCC_COMPILE_FLAGS "-Wall")
set(CMAKE_C_FLAGS  "${CMAKE_CXX_FLAGS} ${GCC_COMPILE_FLAGS}" )

set(CMAKE_EXE_LINKER_FLAGS "-static-libgcc -static-libstdc++")
set(CMAKE_SHARED_LINKER_FLAGS "-static-libgcc -static-libstdc++")
set(CMAKE_STATIC_LINKER_FLAGS "-static-libgcc -static-libstdc++")
```

### add subdirectory to build

```text
add_subdirectory(app)
```

### add library to build

```text
add_library(quickwidgetproxy SHARED quickwidgetproxy.cpp)
```

### add executable

```text
FIND_LIBRARY(PYTHON_LIBRARY python3.5m /depotbld/RHEL6.6/Python-3.5.2/lib)
add_executable(test_serial testserial.cpp)
target_link_libraries (test_serial utils serial ${PYTHON_LIBRARY})
```

* special target properties

  ```text
  set_target_properties( targets...
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
  )
  set_target_properties(dspfpy PROPERTIES PREFIX "")
  ```

### add definitions

```text
add_definitions(-DDEFAULT_CONFIG_FILENAME="default.xml")
```

## QT property

```text
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
```

### qt libray

```text
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)
qt5_use_modules(test_serial Core Widgets)
```

## special utility

### add\_compile\_options\(-fopenmp\)

This option will only influence the compile object file, and if you use

```text
set(GCC_COVERAGE_COMPILE_FLAGS "-fopenmp")
set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS}" )
```

It will also affect in link process.

### add\_custom\_command

#### target

```text
add_custom_command(TARGET MyTarget PRE_BUILD
                   COMMAND ${CMAKE_COMMAND} -E copy_directory
                   ${CMAKE_SOURCE_DIR}/config $<TARGET_FILE_DIR:MyTarget>)
```

#### output

```text
add_custom_command(
  OUTPUT  ${UAVObjectsInit}
  COMMAND  ${CMAKE_BINARY_DIR}/bin/uavobjgenerator -gcs ${CMAKE_SOURCE_DIR}/xml/uavobjectdefinition ${CMAKE_CURRENT_SOURCE_DIR})set(LIBFOO_TAR_HEADERS
  "${CMAKE_CURRENT_BINARY_DIR}/include/foo/foo.h"
  "${CMAKE_CURRENT_BINARY_DIR}/include/foo/foo_utils.h"
)

add_custom_command(OUTPUT ${LIBFOO_TAR_HEADERS}
  COMMAND ${CMAKE_COMMAND} -E tar xzf "${CMAKE_CURRENT_SOURCE_DIR}/libfoo/foo.tar"
  COMMAND ${CMAKE_COMMAND} -E touch ${LIBFOO_TAR_HEADERS}
  WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/include/foo"
  DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/libfoo/foo.tar"
  COMMENT "Unpacking foo.tar"
  VERBATIM
)

add_custom_target(libfoo_untar DEPENDS ${LIBFOO_TAR_HEADERS})

add_library(foo INTERFACE)
target_include_directories(foo INTERFACE "${CMAKE_CURRENT_BINARY_DIR}/include/foo")
target_link_libraries(foo INTERFACE ${FOO_LIBRARIES})
```

### add\_custom\_target

```text
add_custom_target(
  CopyShare ALL
  COMMAND
  ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/share ${PROJECT_BINARY_DIR}/share
  )
```

### include directories

```text
include_directories(${PROJECT_SOURCE_DIR}/mainwindow)
```

### link libraries

for general library in project, just add the library target to target\_link\_libraries, for external library use the following way to get full library and add to target\_link\_libraries, don't use link\_directoris

```text
find_library(PYTHON_LIBRARY python3 HINTS /usr/lib/x86_64-linux-gnu)
target_link_libraries(test aaa PUBLIC ${PYTHON_LIBRARY})
```

## Cmake variables

### CMAKE\_SOURCE\_DIR and PROJECT\_SOURCE\_DIR

* CMAKE\_SOURCE\_DIR : the source directory of cmake
* PROJECT\_SOURCE\_DIR : current project source directory

### CMAKE\_BINARY\_DIR, CMAKE\_CURRENT\_BINARY\_DIR, CMAKE\_CURRENT\_SOURCE\_DIR

* CMAKE\_BINARY\_DIR : the binary directory of cmake
* CMAKE\_CURRENT\_BINARY\_DIR : the current binary directory of cmake
* CMAKE\_CURRENT\_SOURCE\_DIR : the current source directory of cmake

## function and macro

use the following to include macro or function

```text
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")
include(testPilotFunctions)
```

test code

```text
set(var "ABC")

macro(Moo arg)
  message("arg = ${arg}")
  set(arg "abc")
  message("# After change the value of arg.")
  message("arg = ${arg}")
endmacro()
message("=== Call macro ===")
Moo(${var})

function(Foo arg)
  message("arg = ${arg}")
  set(arg "abc")
  message("# After change the value of arg.")
  message("arg = ${arg}")
endfunction()
message("=== Call function ===")
Foo(${var})
```

output

```text
=== Call macro ===
arg = ABC
# After change the value of arg.
arg = ABC
=== Call function ===
arg = ABC
# After change the value of arg.
arg = abc
```

real test case

```text
set (CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")
```

macro file

```text
#CompilePython.cmake
macro(add_python_target tgt)
  foreach(file ${ARGN})
    set(OUT ${CMAKE_BINARY_DIR}/test/${file}c)
    list(APPEND OUT_FILES ${OUT})
    add_custom_command(OUTPUT ${OUT}
      COMMAND ${CMAKE_SOURCE_DIR}/utils/compile.py ${CMAKE_CURRENT_SOURCE_DIR}/${file} ${OUT}
      )
  endforeach()

  add_custom_target(${tgt} ALL DEPENDS ${OUT_FILES})
endmacro()
```

use add\_python\_target

```text
include(CompilePython)
add_python_target(RptDspfRes RptDspfRes.py)
```

conclution: They are string replacements much like the C preprocessor would do with a macro. If you want true CMake variables and/or better CMake scope control you should look at the function command.

## Flex and Bison

Using the follwoing command to treat flex and bison and generate exprlex and exprparse function to use as parser, need to open file to exprin

```text
FIND_PACKAGE(BISON REQUIRED)
SET(BisonOutput ${CMAKE_CURRENT_BINARY_DIR}/parser.cpp)
IF(BISON_FOUND)
    ADD_CUSTOM_COMMAND(
      OUTPUT ${BisonOutput}
      COMMAND ${BISON_EXECUTABLE}
        --defines=${CMAKE_CURRENT_BINARY_DIR}/tokens.h
        --output=${BisonOutput}
    --name-prefix=expr
        ${CMAKE_CURRENT_SOURCE_DIR}/parser.yy
      DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/parser.yy
      COMMENT "Generating parser.cpp"
    )
ENDIF()

FIND_PACKAGE(FLEX REQUIRED)
SET(FlexOutput ${CMAKE_CURRENT_BINARY_DIR}/scanner.cpp)
IF(FLEX_FOUND)
    ADD_CUSTOM_COMMAND(
      OUTPUT ${FlexOutput}
      COMMAND ${FLEX_EXECUTABLE}
        --outfile=${FlexOutput}
        --prefix=expr
        ${CMAKE_CURRENT_SOURCE_DIR}/scanner.ll
      DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/scanner.ll
      COMMENT "Generating scanner.cpp"
    )
ENDIF()
```

## Misc

For create a static library combines from several static libary, it will need the help from ar. Like the following case we want to create a libntv.a for lib\*.a, then we need to write a script.ar

```text
> ls
libbasic.a  libnetlist.a  libntv1.a     libutil.a
libgui.a    libntv.a      libtreemap.a  script.ar
[ktc@fshi502 lib]> more script.ar 
CREATE libntv.a
ADDLIB libbasic.a
ADDLIB libgui.a
ADDLIB libnetlist.a
ADDLIB libntv1.a
ADDLIB libtreemap.a
ADDLIB libutil.a
SAVE
END
[ktc@fshi502 lib]> /depot/qsc/QSCP/binutils/bin/ar -M < script.ar
```

* ar: create, modify, and extract from archives
* ranlib: generate index to archive

