# Minimal test case for boost iostreams
# Use ExternalProject to build boost as a third party library, with a user 
# compiled bzip2, libz, and lzma.

CMAKE_MINIMUM_REQUIRED(VERSION 3.2.3)

project(boost_iostreams_test C CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

set(CMAKE_CXX_FLAGS "-march=native -fPIC")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -msse2 -mssse3 -msse4.1 -msse4.2")

set(CMAKE_C_FLAGS "-march=native -fPIC")
set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS} -O3 -msse2 -mssse3 -msse4.1 -msse4.2")

include(ProcessorCount)

ProcessorCount(N)
if(NOT N EQUAL 0)
  set(CTEST_BUILD_FLAGS -j${N})
  set(ctest_test_args ${ctest_test_args} PARALLEL_LEVEL ${N})
endif()

message(STATUS "Using ${N} cores to build boost.")

add_subdirectory(vendors)

