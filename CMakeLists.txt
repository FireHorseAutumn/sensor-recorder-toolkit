cmake_minimum_required(VERSION 3.20)
project(sensor-recorder-toolkit LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# CUDA optional: only needed for B3 pinned pool
find_package(CUDAToolkit QUIET)
# liburing / FUSE3 optional: only needed for B2/B4
find_package(PkgConfig QUIET)
if(PkgConfig_FOUND)
    pkg_check_modules(LIBURING QUIET liburing)
    pkg_check_modules(FUSE3 QUIET fuse3)
endif()

add_library(srt INTERFACE)
target_include_directories(srt INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

# TODO: add targets as sources land
# add_executable(test_round_trip tests/test_round_trip.cpp)
# add_executable(bench_io benchmarks/bench_io.cpp)

enable_testing()
