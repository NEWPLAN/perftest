CMAKE_MINIMUM_REQUIRED(VERSION 3.0) #cmake版本要求
project(MyPerfTest)

add_definitions(-DHAVE_CONFIG_H -D_GNU_SOURCE)

message("Building make for Hello") #打印消息
message("add -DDYNAMIC_E=1 when build dynamic class E, the default is static")
set(CMAKE_BUILD_TYPE "Debug")

set(SUB_MODULE_PATH "./include")
set(CMAKE_CXX_STANDARD 14) # 设置std=c++11
set(CMAKE_C_STANDARD 11)

# 设置debug或者release的编译参数
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb -fPIC")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall -fPIC")

set(CMAKE_C_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -g -Wall -ggdb -fPIC")
set(CMAKE_C_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall -fPIC")

# collect all message to "SRC_LIST" <-- ./*.cpp
aux_source_directory(./src SRC_LIST)

#分别指定编译include和链接link的搜索目录
include_directories(${SUB_MODULE_PATH})
link_directories(${SUB_MODULE_PATH})

#添加一个子模块，编译到lib目录下去
add_subdirectory(./lib lib) # add a submodule, and compile it to the "build/lib"
foreach(target ${SRC_LIST})
    # message(${target})
    STRING(REGEX REPLACE ".+/(.+)\\..*" "\\1" FILE_NAME ${target})
    MESSAGE("Building target for: " ${FILE_NAME})
    #设置编译目标
    add_executable(${FILE_NAME} ${target})
    #添加编译目标依赖
    target_link_libraries(${FILE_NAME}
                        static_perftest
                        m
                        mlx5
                        rdmacm
                        ibumad
                        ibverbs
                        pthread)

endforeach()
