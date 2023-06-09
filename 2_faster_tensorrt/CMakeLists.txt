cmake_minimum_required(VERSION 3.0)

project(yolo_trt)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Debug)

find_package(CUDA REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${PROJECT_SOURCE_DIR}/include)

# cuda
include_directories(/usr/local/cuda-10.2/include)
link_directories(/usr/local/cuda-10.2/lib64)
# cudnn
include_directories(/home/zzx/Github/zzx_yolo/EXTRA_PKG/cudnn8.7.0.84_cuda10/include)
link_directories(/home/zzx/Github/zzx_yolo/EXTRA_PKG/cudnn8.7.0.84_cuda10/lib)
# tensorrt
include_directories(/home/zzx/Github/zzx_yolo/EXTRA_PKG/TensorRT-8.5.3.1/include)
link_directories(/home/zzx/Github/zzx_yolo/EXTRA_PKG/TensorRT-8.5.3.1/lib)

set(CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -O0 -Wfatal-errors -pthread -w -g")
set(CUDA_NVCC_FLAGS "${CUDA_NVCC_FLAGS} -std=c++11 -O0 -Xcompiler -fPIC -g -w ${CUDA_GEN_CODE}")

file(GLOB_RECURSE cpp_srcs ${PROJECT_SOURCE_DIR}/src/*.cpp)
file(GLOB_RECURSE cuda_srcs ${PROJECT_SOURCE_DIR}/src/*.cu)

cuda_add_library(plugin_list SHARED ${cuda_srcs})
target_link_libraries(plugin_list nvinfer nvinfer_plugin)
target_link_libraries(plugin_list cuda cublas cudart cudnn)
target_link_libraries(plugin_list pthread)
target_link_libraries(plugin_list ${OpenCV_LIBS})

add_executable(yolo ${cpp_srcs})
target_link_libraries(yolo plugin_list)
target_link_libraries(yolo nvinfer nvinfer_plugin nvonnxparser)
target_link_libraries(yolo cuda cublas cudart cudnn)
target_link_libraries(yolo pthread)
target_link_libraries(yolo ${OpenCV_LIBS})
