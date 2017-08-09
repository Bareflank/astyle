cmake_minimum_required(VERSION 2.8.4)
project(astyle CXX)

# Version
set(MAJOR_VERSION 3)
set(MINOR_VERSION 0)
set(PATCH_VERSION 0)

# Options
option(BUILD_JAVA_LIBS "Build shared library" OFF)
option(BUILD_SHARED_LIBS "Build shared library" OFF)
option(BUILD_STATIC_LIBS "Build static library" OFF)

# If a java library is requested, shared libraries should be enabled
# Also, make sure java is installed
if(BUILD_JAVA_LIBS)
    if(NOT EXISTS /usr/lib/jvm/default-java/include)
        message(FATAL_ERROR "jvm-dev not installed")
    endif()
    set(BUILD_SHARED_LIBS ON)
endif()

# Executable Sources
list(APPEND exe_SRCS
    src/ASBeautifier.cpp
    src/ASEnhancer.cpp
    src/ASFormatter.cpp
    src/ASLocalizer.cpp
    src/ASResource.cpp
    src/astyle_main.cpp
)

# Library Sources
list(APPEND lib_SRCS
    src/ASBeautifier.cpp
    src/ASEnhancer.cpp
    src/ASFormatter.cpp
    src/ASResource.cpp
    src/astyle_main.cpp
)

# Release build by default
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

# Define the output type
if(BUILD_SHARED_LIBS OR BUILD_STATIC_LIBS)
    add_library(astyle ${lib_SRCS})
    install(TARGETS astyle DESTINATION lib)
else()
    add_executable(astyle ${exe_SRCS})
    install(TARGETS astyle DESTINATION bin)
endif()

# Default compile options
if(NOT WIN32 STREQUAL "1")
    target_compile_options(astyle PRIVATE -Wall -fno-rtti -fno-exceptions -std=c++11)
else()
    target_compile_options(astyle PRIVATE /EHsc)
endif()

# Turn off debugging on release builds
if(CMAKE_BUILD_TYPE STREQUAL "Release")
    target_compile_options(astyle PRIVATE -DNDEBUG)
endif()

# Build specific compile options
if(BUILD_SHARED_LIBS OR BUILD_STATIC_LIBS)
    if(BUILD_JAVA_LIBS)
        target_compile_options(astyle PRIVATE -DASTYLE_JNI)
        target_compile_options(astyle PRIVATE -I/usr/lib/jvm/default-java/include )
        target_compile_options(astyle PRIVATE -I/usr/lib/jvm/default-java/include/linux )
    else()
        target_compile_options(astyle PRIVATE -DASTYLE_LIB)
    endif()
    set_property(TARGET astyle PROPERTY VERSION ${MAJOR_VERSION}.${MINOR_VERSION}.${PATCH_VERSION})
    set_property(TARGET astyle PROPERTY SOVERSION ${MAJOR_VERSION})
endif()
