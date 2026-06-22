cmake_minimum_required(VERSION 3.12)
project(yolov11-tensorrt)

find_package(CUDA REQUIRED)
# ==================== 新增 ====================
# 1. 允许 NVCC 配合新版 VS 编译器（解决上一步报错）
list(APPEND CUDA_NVCC_FLAGS "-allow-unsupported-compiler")

# 2. 新增：允许 NVCC 编译时使用不匹配的 STL 版本（解决当前的 STL1002 报错）
list(APPEND CUDA_NVCC_FLAGS "-D_ALLOW_COMPILER_AND_STL_VERSION_MISMATCH")

# 3. 新增：允许 MSVC 宿主编译器在编译 C++ 文件时也跳过 STL 版本检查
add_definitions(-D_ALLOW_COMPILER_AND_STL_VERSION_MISMATCH)
# =======================================================

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
add_definitions(-DAPI_EXPORTS)

# Include CUDA directories
include_directories(${CUDA_INCLUDE_DIRS})
include_directories(src/)

# Add source files
set(SOURCES
    main.cpp
    src/yolov11.cpp
)

# Add CUDA source files
set(CUDA_SOURCES src/preprocess.cu)

# Add headers
set(HEADERS
    src/yolov11.h
    src/macros.h
    src/logging.h
    src/cuda_utils.h
    src/preprocess.h
    src/common.h
)

# Find and include OpenCV
set(OpenCV_DIR "E:/software_E/opencv/4_10/opencv/build")

# Set TensorRT path if not set in environment variables
set(TENSORRT_DIR "E:/software_E/tensorrt/TensorRT-8.6.1.6.Windows10.x86_64.cuda-11.8/TensorRT-8.6.1.6")

# Include OpenCV
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

# Include TensorRT
include_directories(${TENSORRT_DIR}/include)
link_directories(${TENSORRT_DIR}/lib)
set(TENSORRT_LIBS nvinfer nvinfer_plugin nvparsers nvonnxparser)

# Create an executable
cuda_add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS} ${CUDA_SOURCES})

# Link libraries
target_link_libraries(${PROJECT_NAME}
    ${OpenCV_LIBS}
    ${CUDA_LIBRARIES}
    ${TENSORRT_LIBS}
)