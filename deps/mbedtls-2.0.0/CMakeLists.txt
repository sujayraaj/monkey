cmake_minimum_required(VERSION 2.6)
project("mbed TLS" C)

option(USE_PKCS11_HELPER_LIBRARY "Build mbed TLS with the pkcs11-helper library." OFF)
option(ENABLE_ZLIB_SUPPORT "Build mbed TLS with zlib library." OFF)

option(ENABLE_PROGRAMS "Build mbed TLS programs." OFF)


# the test suites currently have compile errors with MSVC
if(MSVC)
    option(ENABLE_TESTING "Build mbed TLS tests." OFF)
else()
    option(ENABLE_TESTING "Build mbed TLS tests." ON)
endif()

set(CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE}
    CACHE STRING "Choose the type of build: None Debug Release Coverage ASan ASanDbg MemSan MemSanDbg Check CheckFull"
    FORCE)

string(REGEX MATCH "Clang" CMAKE_COMPILER_IS_CLANG "${CMAKE_C_COMPILER_ID}")

if(CMAKE_COMPILER_IS_GNUCC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -W -Wdeclaration-after-statement -Wwrite-strings -Wlogical-op")
    set(CMAKE_C_FLAGS_RELEASE     "-O2")
    set(CMAKE_C_FLAGS_DEBUG       "-O0 -g3")
    set(CMAKE_C_FLAGS_COVERAGE    "-O0 -g3 --coverage")
    set(CMAKE_C_FLAGS_ASAN        "-Werror -fsanitize=address -fno-common -O3")
    set(CMAKE_C_FLAGS_ASANDBG     "-Werror -fsanitize=address -fno-common -O1 -g3 -fno-omit-frame-pointer -fno-optimize-sibling-calls ")
    set(CMAKE_C_FLAGS_CHECK       "-Werror -Os")
    set(CMAKE_C_FLAGS_CHECKFULL   "${CMAKE_C_FLAGS_CHECK} -Wcast-qual")
endif(CMAKE_COMPILER_IS_GNUCC)

if(CMAKE_COMPILER_IS_CLANG)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -W -Wdeclaration-after-statement -Wwrite-strings -Wpointer-arith")
    set(CMAKE_C_FLAGS_RELEASE     "-O2")
    set(CMAKE_C_FLAGS_DEBUG       "-O0 -g3")
    set(CMAKE_C_FLAGS_COVERAGE    "-O0 -g3 --coverage")
    set(CMAKE_C_FLAGS_ASAN        "-Werror -fsanitize=address -fno-common -fsanitize=undefined -fno-sanitize-recover -O3")
    set(CMAKE_C_FLAGS_ASANDBG     "-Werror -fsanitize=address -fno-common -fsanitize=undefined -fno-sanitize-recover -O1 -g3 -fno-omit-frame-pointer -fno-optimize-sibling-calls ")
    set(CMAKE_C_FLAGS_MEMSAN      "-Werror -fsanitize=memory -O3")
    set(CMAKE_C_FLAGS_MEMSANDBG   "-Werror -fsanitize=memory -O1 -g3 -fno-omit-frame-pointer -fno-optimize-sibling-calls -fsanitize-memory-track-origins=2")
    set(CMAKE_C_FLAGS_CHECK       "-Werror -Os")
endif(CMAKE_COMPILER_IS_CLANG)

if(MSVC)
    set(CMAKE_C_FLAGS_CHECK       "/WX")
endif(MSVC)

if(CMAKE_BUILD_TYPE STREQUAL "Coverage")
    if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_CLANG)
        set(CMAKE_SHARED_LINKER_FLAGS "--coverage")
    endif(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_CLANG)
endif(CMAKE_BUILD_TYPE STREQUAL "Coverage")

if(LIB_INSTALL_DIR)
else()
    set(LIB_INSTALL_DIR lib)
endif()

include_directories(include/)

if(ENABLE_ZLIB_SUPPORT)
    find_package(ZLIB)

    if(ZLIB_FOUND)
        include_directories(${ZLIB_INCLUDE_DIR})
    endif(ZLIB_FOUND)
endif(ENABLE_ZLIB_SUPPORT)

add_subdirectory(library)
add_subdirectory(include)
