cmake_minimum_required(VERSION 3.18)
project(tensorrt_cpp_api)

# Use ccache to speed up rebuilds
include(cmake/ccache.cmake)

# Set C++ version and optimization level
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Ofast -DNDEBUG -Wno-deprecated-declarations")

# For finding FindTensorRT.cmake
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake" ${CMAKE_MODULE_PATH})

# We require CUDA, OpenCV, and TensorRT
find_package(TensorRT REQUIRED)
find_package(OpenCV REQUIRED)
find_package(fmt REQUIRED)

add_library(tensorrt_cpp_api SHARED
        src/engine.cpp)

target_include_directories(tensorrt_cpp_api PUBLIC ${OpenCV_INCLUDE_DIRS} ${CUDA_INCLUDE_DIRS} ${TensorRT_INCLUDE_DIRS} include include/interfaces)
target_compile_definitions(tensorrt_cpp_api
  INTERFACE
    FMT_HEADER_ONLY=1

    FMT_USE_LONG_DOUBLE=0
    FMT_USE_INT128=0
    FMT_USE_FLOAT128=0
    FMT_STATIC_THOUSANDS_SEPARATOR=1
)
target_link_libraries(tensorrt_cpp_api PUBLIC ${OpenCV_LIBS} ${CUDA_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT} ${TensorRT_LIBRARIES})

add_executable(run_inference_benchmark src/main.cpp)
target_compile_definitions(run_inference_benchmark
  INTERFACE
    FMT_HEADER_ONLY=1

    FMT_USE_LONG_DOUBLE=0
    FMT_USE_INT128=0
    FMT_USE_FLOAT128=0
    FMT_STATIC_THOUSANDS_SEPARATOR=1
)
target_link_libraries(run_inference_benchmark tensorrt_cpp_api)
