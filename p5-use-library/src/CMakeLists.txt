#允许动态库路径使用相对路径
#https://cmake.org/cmake/help/v3.0/policy/CMP0015.html
cmake_policy(SET CMP0015 NEW)

#添加头文件路径
INCLUDE_DIRECTORIES(../../p4-library/lib)
#添加动态库路径
LINK_DIRECTORIES(../../p4-library/build/lib)

ADD_EXECUTABLE(main main.c)

#链接动态库
TARGET_LINK_LIBRARIES(main hello)
