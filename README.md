#Redis CMakeLists

This README is just a simplify specification to use CMakeLists.txt in redis project

Why using CMakeList.txt
--------------
Refer to the Issue: https://github.com/redis/redis/issues/1761. Some one guy want to run the Redis on his 
Xcode(An IDE on Mac OS).

And the semi-official Answer: https://github.com/redis/redis/issues/1761#issuecomment-44295750 .
As a conclusion, NOPE NOPE NODE. It is impossible to include CMakeList.txt in official source code project

Lots of developer use the Jetbrains IDE to develop projects Which depend on the CMakeList.txt file 
to build the source C/C++ source code.

That is the reason for the effort. The non-official approach to use CMake and CMakeList.txt for building and testing Redis source code

What is CMake?
--------------

CMake is an open-source, cross-platform family of tools designed to build, test and package software. 

CMake is used to control the software compilation process using simple platform and compiler independent configuration files, 
and generate native makefiles and workspaces that can be used in the compiler environment of your choice. 

The suite of CMake tools were created by Kitware in response to the need for a powerful, cross-platform build environment for open-source projects such as ITK and VTK.

Using CMake in Redis
--------------

1.Add five CMakeLists.txt files (and more in the future) into the Redis project dir:

    redis/CMakeLists.txt
    redis/src/modules/CMakeLists.txt
    redis/deps/CMakeLists.txt
    redis/deps/linenoise/CMakeLists.txt
    redis/deps/lua/CMakeLists.txt
2.Change the source file

    //modify: redis/src/ae_kqueue.c  add include files:
    #include "ae.h"
    #include "zmalloc.h"

the every CMakeLists.txt file content you can refer to this repo and branch: feature/add-cmakefiles,
then you can debug the Redis process step by step in IDE (such as CLion)

3.shell command:

    export  REDIS_SOURCE_CODE_PATH="Please change this string to your redis source code path"
    cmake -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - Unix Makefiles" ${REDIS_SOURCE_CODE_PATH}
    cmake --build ${REDIS_SOURCE_CODE_PATH}/cmake-build-debug --target redis-server -- -j 12
    # This step lead to an error :ld: symbol(s) not found for architecture x86_64 clang: error: linker command failed with exit code 1 (use -v to see invocation)
    # so We should play a trick to the linker
    echo "set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -c")" >> ${REDIS_SOURCE_CODE_PATH}/CMakeLists.txt
    # Then try again
    cmake --build ${REDIS_SOURCE_CODE_PATH}/cmake-build-debug --target redis-server -- -j 12

Condition
--------------
OS:
  Darwin-19.6.0
compiler:
  cc -v
  Apple clang version 12.0.0 (clang-1200.0.32.28)
  Target: x86_64-apple-darwin19.6.0
  Thread model: posix  

Enjoy!

TODO List
--------------
1. compile the source code on linux (such as a Distributions: Ubuntu)
2. compile the source code on windows(Event though it is not friendly for developer to work on it)
