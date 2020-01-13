
https://github.com/xiangweizeng/darknet2ncnn

sudo apt-get install pkg-config

sudo apt install libcanberra-gtk-module



qt编译部署相关问题：当主机编译的可执行文件拷贝到目标机报qt_5.13 not found 之类的的错误时候，需要修改LD_LIBRARY_PATH环境变量到QT的lib
export LD_LIBRARY_PATH=/home/md/Qt5.13.2/5.13.2/gcc_64/lib




# CMakeLists.txt

```bash


# 最低版本要求
cmake_minimum_required(VERSION 3.4.1)

project(darknetYoloV3NCNN)

# 设置C++编译版本
set(CMAKE_CXX_STANDARD 11)

# ncnn项目所在路径，需要替换
set(NCNN_DIR /home/ray/ncnn)
set(DARKNET_DIR /home/ray/darknet2ncnn)


# 分别设置ncnn的链接库和头文件
set(NCNN_LIBS ${NCNN_DIR}/build/install/lib/libncnn.a ${DARKNET_DIR}/libdarknet2ncnn.a)
set(NCNN_INCLUDE_DIRS ${NCNN_DIR}/build/install/include/ncnn ${DARKNET_DIR}/include ${NCNN_DIR}/src)


# 配置OpenCV
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

include_directories(${NCNN_INCLUDE_DIRS})

# 配置OpenMP
FIND_PACKAGE( OpenMP REQUIRED)  
if(OPENMP_FOUND)  
    message("OPENMP FOUND")  
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")  
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")  
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")  
endif()  


# 建立链接依赖
add_executable(darknetYoloV3NCNN Main.cpp)
target_link_libraries(darknetYoloV3NCNN ${NCNN_LIBS})
target_link_libraries(darknetYoloV3NCNN ${OpenCV_LIBS})


```
