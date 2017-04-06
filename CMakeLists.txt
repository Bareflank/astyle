cmake_minimum_required(VERSION 3.4)
project(astyle C CXX)

list(APPEND SOURCES
    src/ASBeautifier.cpp
    src/ASEnhancer.cpp
    src/ASFormatter.cpp
    src/ASLocalizer.cpp
    src/ASResource.cpp
    src/astyle_main.cpp
)

if(NOT WIN32 STREQUAL "1")
    set(CMAKE_C_FLAGS "-DNDEBUG -O3 -Wall -Wextra -fno-rtti -fno-exceptions")
    set(CMAKE_CXX_FLAGS "-DNDEBUG -O3 -Wall -Wextra -fno-rtti -fno-exceptions")
else()
    set(CMAKE_C_FLAGS "-DNDEBUG")
    set(CMAKE_CXX_FLAGS "-DNDEBUG")
endif()

add_executable(astyle ${SOURCES})

install(TARGETS astyle DESTINATION bin)
