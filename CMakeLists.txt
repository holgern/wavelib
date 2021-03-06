cmake_minimum_required(VERSION 2.8.0 FATAL_ERROR)

set(PROJECT_NAME wavelib)
project(${PROJECT_NAME} CXX C)


# src root path
set(WAVELIB_SRC_ROOT ${PROJECT_SOURCE_DIR} CACHE PATH "Wavelib source root")
# binary output by default
set(COMMON_BIN_PATH ${CMAKE_BINARY_DIR}/Bin)
set(LIBRARY_OUTPUT_PATH ${COMMON_BIN_PATH}/${CMAKE_BUILD_TYPE})
set(EXECUTABLE_OUTPUT_PATH ${COMMON_BIN_PATH}/${CMAKE_BUILD_TYPE})
# set where to find additional cmake modules if any
set(CMAKE_MODULE_PATH ${WAVELIB_SRC_ROOT}/cmake ${CMAKE_MODULE_PATH})


set(WAVELIB_VERSION "1.0.0" CACHE STRING "Wavelib version" FORCE)
message(">>> Building Wavelib version: ${WAVELIB_VERSION}")
message(">>> EXECUTABLE_OUTPUT_PATH = ${EXECUTABLE_OUTPUT_PATH}")

option(BUILD_UT "Enable Unit test" ON)

# cleanup prefix lib for Unix-like OSes
set(CMAKE_SHARED_MODULE_PREFIX)

# install target to this folder by default
set(WAVELIB_BINARY_DIR ${WAVELIB_SRC_ROOT}/bin)
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "${WAVELIB_BINARY_DIR}" CACHE PATH "default install path" FORCE)
endif()

# make include globaly visible
set(PROJECT_WIDE_INCLUDE ${WAVELIB_SRC_ROOT}/include)
include_directories(${PROJECT_WIDE_INCLUDE})

# TODO: need to handle this with option
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

set(Boost_USE_STATIC_LIBS ${USE_STATIC_BOOST})
set(Boost_USE_MULTITHREADED ON)
unset(Boost_INCLUDE_DIR CACHE)
unset(Boost_LIBRARY_DIRS CACHE)
find_package(Boost REQUIRED COMPONENTS unit_test_framework)
if(USE_STATIC_BOOST)
   message(STATUS "Linking against boost static libraries")
else(USE_STATIC_BOOST)
   message(STATUS "Linking against boost dynamic libraries")
endif(USE_STATIC_BOOST)

    # disable autolinking feature
add_definitions(-DBOOST_ALL_NO_LIB)
include_directories(${Boost_INCLUDE_DIRS})


include_directories(${COMMON_BIN_PATH})

if(BUILD_UT)
    include(CTest)
    enable_testing()
    add_subdirectory(unitTests)
endif()

add_subdirectory(src)

install(DIRECTORY ${WAVELIB_SRC_ROOT}/include/ DESTINATION include FILES_MATCHING PATTERN "*.h")