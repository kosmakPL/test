project(basic_test)

cmake_minimum_required(VERSION 3.14)
set(CMAKE_CXX_STANDARD 11)
set(GTEST_VERSION 1.11.0 CACHE STRING "Google test version")
set(CMAKE_CXX_FLAGS_RELEASE_INIT "-Wall")

include(FetchContent)

################################
# GTest
################################
FetchContent_Declare(googletest
GIT_REPOSITORY https://github.com/google/googletest.git
GIT_TAG release-${GTEST_VERSION})

FetchContent_GetProperties(googletest)
FetchContent_Populate(googletest)
add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
set(BUILD_GMOCK ON CACHE BOOL "" FORCE)
set(BUILD_GTEST ON CACHE BOOL "" FORCE)

FetchContent_MakeAvailable(googletest)

set(EVENT__DISABLE_OPENSSL ON CACHE BOOL "" FORCE)
set(EVENT__DISABLE_MBEDTLS ON CACHE BOOL "" FORCE)
set(EVENT__DISABLE_TESTS ON CACHE BOOL "" FORCE)

FetchContent_Declare(
  glog
  URL https://github.com/google/glog/archive/refs/heads/master.zip
)
FetchContent_MakeAvailable(glog)

if(NOT glog_POPULATED)
  FetchContent_Populate(glog)
  FetchContent_MakeAvailable(glog)
add_subdirectory(${glog_SOURCE_DIR} ${lglog_BINARY_DIR})
include_directories(${glog_SOURCE_DIR}/include)
include_directories(${glog_BINARY_DIR}/include)
endif()

FetchContent_Declare(
  libevent
  URL https://github.com/libevent/libevent/archive/refs/heads/master.zip
)

if(NOT libevent_POPULATED)
  FetchContent_Populate(libevent)
  FetchContent_MakeAvailable(libevent)
add_subdirectory(${libevent_SOURCE_DIR} ${libevent_BINARY_DIR})
include_directories(${libevent_SOURCE_DIR}/include)
include_directories(${libevent_BINARY_DIR}/include)
endif()

set(EVPP_VCPKG_BUILD ON CACHE BOOL "" FORCE)

FetchContent_Declare(
  evpp
  URL https://github.com/Qihoo360/evpp/archive/refs/heads/master.zip
)
FetchContent_MakeAvailable(evpp)

enable_testing()

################################
# Unit Tests
################################
# Add test cpp file
add_executable(
  hello_test
  hello_test.cc
)

# Link test executable against gtest & gtest_main
target_link_libraries(hello_test gtest gtest_main)

add_test(hello_test hello_test)
