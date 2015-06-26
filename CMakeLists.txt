cmake_minimum_required(VERSION 2.8)

project(selective_search)

# default build type is Release
set(CMAKE_BUILD_TYPE "Release")

# enable c++0x / c++11
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag("-std=c++11" COMPILER_SUPPORTS_CXX11)
check_cxx_compiler_flag("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
  message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different c++ compiler.")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pedantic-errors -Wno-unused-function")
# use march=native option to enable SSE4.2
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -march=native")

# add Boost and Boost.Python packages
find_package(Boost 1.46 COMPONENTS system filesystem python REQUIRED)
include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})
find_package(PythonInterp REQUIRED)
find_package(PythonLibs REQUIRED)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_LIBS ON)
include_directories(${PYTHON_INCLUDE_DIRS})

# add python modules
include_directories("include")
python_add_module(segment src/segment_py.cpp)
target_link_libraries(segment ${PYTHON_LIBRARIES} ${Boost_LIBRARIES} boost_numpy)

# install
install(TARGETS segment LIBRARY DESTINATION "${CMAKE_SOURCE_DIR}/selective_search")
