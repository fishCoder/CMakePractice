# CMake 极简入门

>本文根据《[cmake实践.pdf](./cmake实践.pdf)》整理并简化一些内容 

#### [项目地址](https://github.com/fishCoder/CMakePractice)

******

>p1 [最简单的hello world](./p1-hello)


文件结构(其他均为cmake生成的文件)

CMakeLists.txt

main.c

***main.c***

```
#include <stdio.h>
int main(){
    printf("Hello World use Cmake :)\n");
	return 0;
}
```

***CMakeLists.txt***

```
#设置项目名称 HELLO
PROJECT (HELLO)

#变量SRC_LIST 赋值 main.c
SET(SRC_LIST main.c)

#MESSAGE 输出log 
#SEND_ERROR，产生错误，生成过程被跳过
#SATUS，输出前缀为—的信息
#FATAL_ERROR，立即终止所有 cmake 过程
#${}取出变量值
MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR}) 


#隐式变量 HELLO_SOURCE_DIR 即  <项目名称>_SOURCE_DIR
MESSAGE(STATUS "This is SOURCE dir " ${HELLO_SOURCE_DIR}) 

#ADD_EXECUTABLE 生产执行文件
ADD_EXECUTABLE(hello ${SRC_LIST})
```

***编译运行***

```
cmake

make

./hello
```


>p2 [多个文件的hello world](./p2-command)

CMakeLists.txt

main.c
	
fun.h
	
fun.c

******

***fun.h***

```
#include <stdio.h>
void fun();
```

***fun.c***

```
void fun(){
    printf("call fun \n");
}

```

***main.c***

```
#include <stdio.h>
#include "fun.h"

int main(){
    fun();
    return 0;
}

```


***CMakeLists.txt***

```
PROJECT(P2_COMMAND)

#变量赋值
SET(PARAM 'this is param')

#赋多个值 给变量SRC
SET(SRC main.c fun.h fun.c)

MESSAGE(STATUS 'PARAM :' ${PARAM})
MESSAGE(STATUS 'SRC:' ${SRC})

#生成执行文件fun
ADD_EXECUTABLE(fun ${SRC})
```
***编译运行***

```
cmake

make

./hello
```



>p3 [结构化的hello world](./p3-helloworld)

CMakeLists.txt

src\main.c CMakeLists.txt

bin\

******

***CMakeLists.txt***

```
PROJECT(HELLO) 

#添加子目录 并在该目录下自动寻找CMakeLists.txt 运行
ADD_SUBDIRECTORY(src bin)

#EXECUTABLE_OUTPUT_PATH 常量变量 输出可执行路径
#PROJECT_BINARY_DIR 常量变量
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)

#LIBRARY_OUTPUT_PATH 动态库输出路径
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

MESSAGE(STATUS EXECUTABLE_OUTPUT_PATH ${EXECUTABLE_OUTPUT_PATH})
MESSAGE(STATUS LIBRARY_OUTPUT_PATH ${LIBRARY_OUTPUT_PATH})


```

***src\CMakeLists.txt***

```
ADD_EXECUTABLE(hello main.c)

```

***编译运行***

```
cd build

cmake ..

make

./hello

```


>p4 [编译动态库 并且链接库](./p4-library)

CMakeLists.txt

bin/

lib/CMakeLists.txt	hello.c		hello.h

******

***lib/hello.h***

```
#ifndef HELLO_H
#define HELLO_H

#include <stdio.h>
void sayHello();
#endif


```

***lib/hello.c***

```
#include "hello.h"

void sayHello(){
    printf("Hello World :) \n");
}


```


***CMakeLists.txt***

```
PROJECT(HELLOLIB)

ADD_SUBDIRECTORY(lib)
```

***lib/CMakeLists.txt***

```
SET(LIBHELLO_SRC hello.c)


ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
#ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})

#静态库hello_static重命名输出hello
SET_TARGET_PROPERTIES(hello_static PROPERTIES OUTPUT_NAME "hello")

#OUTPUT_VALUE 赋值 hello_static. OUTPUT_NAME
GET_TARGET_PROPERTY(OUTPUT_VALUE hello_static OUTPUT_NAME)
MESSAGE(STATUS "This is the hello_static OUTPUT_NAME:"${OUTPUT_VALUE})

#设置版本信息
SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)

#安装指令
#INSTALL(TARGETS hello hello_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)

```

***编译运行***

```
cd build
cmake ..
make
```


>p5 [链接外部库](./p5-use-library)

CMakeLists.txt		
build/		
src/CMakeLists.txt	main.c

******

***CMakeLists.txt***

```
PROJECT(NEWHELLO)
ADD_SUBDIRECTORY(src)
```

***src/main.c***

```
#include <stdio.h>
#include "hello.h"

int main(){
    sayHello();
    return 0;
}

```

***src/CMakeLists.txt***

```
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
```


>p6 [cmake一些基本常量以及语法](./p6-cmake-constant)

CMakeLists.txt	

anthor\CMakeLists.txt	src1.c		src2.c	 src3.c

src\CMakeLists.txt

build\

******

***CMakeLists.txt***

```
PROJECT(CONSTANT)
MESSAGE(STATUS "CMAKE_BINARY_DIR=" ${CMAKE_BINARY_DIR})
MESSAGE(STATUS "PROJECT_BINARY_DIR=" ${PROJECT_BINARY_DIR})
MESSAGE(STATUS "CONSTANT_BINARY_DIR=" ${CONSTANT_BINARY_DIR})

#当前CMakeLists.txt路径
MESSAGE(STATUS "CMAKE_CURRENT_SOURCE_DIR=" ${CMAKE_CURRENT_SOURCE_DIR})
MESSAGE(STATUS "CMAKE_CURRRENT_BINARY_DIR=" ${CMAKE_CURRRENT_BINARY_DIR})
MESSAGE(STATUS "CMAKE_CURRENT_LIST_FILE=" ${CMAKE_CURRENT_LIST_FILE})
MESSAGE(STATUS "CMAKE_CURRENT_LIST_LINE=" ${CMAKE_CURRENT_LIST_LINE})
MESSAGE(STATUS "CMAKE_MODULE_PATH=" ${CMAKE_MODULE_PATH})

ADD_SUBDIRECTORY(src)
ADD_SUBDIRECTORY(anthor)
```

***src/CMakeLists.txt***

```
MESSAGE(STATUS "THIS IS SRC")

MESSAGE(STATUS "CMAKE_BINARY_DIR=" ${CMAKE_BINARY_DIR})
MESSAGE(STATUS "PROJECT_BINARY_DIR=" ${PROJECT_BINARY_DIR})
MESSAGE(STATUS "CONSTANT_BINARY_DIR=" ${CONSTANT_BINARY_DIR})


MESSAGE(STATUS "CMAKE_SOURCE_DIR=" ${CMAKE_SOURCE_DIR})
MESSAGE(STATUS "PROJECT_SOURCE_DIR=" ${PROJECT_SOURCE_DIR})
MESSAGE(STATUS "CONSTANT_SOURCE_DIR=" ${CONSTANT_SOURCE_DIR})

MESSAGE(STATUS "CMAKE_CURRENT_SOURCE_DIR=" ${CMAKE_CURRENT_SOURCE_DIR})
MESSAGE(STATUS "CMAKE_CURRRENT_BINARY_DIR=" ${CMAKE_CURRRENT_BINARY_DIR})
MESSAGE(STATUS "CMAKE_CURRENT_LIST_FILE=" ${CMAKE_CURRENT_LIST_FILE})
MESSAGE(STATUS "CMAKE_CURRENT_LIST_LINE=" ${CMAKE_CURRENT_LIST_LINE})

```

***anthor/CMakeLists.txt***

```
MESSAGE(STATUS "THIS IS ANTHOR") 

#检测当前编译平台
#判断语句
IF(WIN32)
    MESSAGE(STATUS "SYSTEM IS WIN32")
ELSEIF(UNIX)
    MESSAGE(STATUS "SYSTEM IS UNIX")
ELSEIF(APPLE)
    MESSAGE(STATUS "SYSTEM IS APPLE")
ENDIF(WIN32)

#将当前文件夹的文件 添加到变量SRC_LIST
AUX_SOURCE_DIRECTORY(. SRC_LIST)

#循环语句
FOREACH(VAR ${SRC_LIST}) 
    MESSAGE(${VAR}) 
ENDFOREACH(VAR)

```





>p7 [使用FIND_PACKAGE查找模块](./p7-find-module)

CMakeLists.txt	

build/		

src/CMakeLists.txt	main.c

******

***CMakeLists.txt***

```
PROJECT(CURLTEST)
ADD_SUBDIRECTORY(src)
```

***src/main.c***

调用外部curl库 获取网页 并保存下来

```
#include <stdlib.h>
#include <unistd.h>

FILE *fp;
int write_data(void *ptr, size_t size, size_t nmemb, void *stream){
    int written = fwrite(ptr,size,nmemb,(FILE *)fp);
    return written;
}

int main(){
    const char *path = "./curl-test";
    const char *mode = "w";
    fp = fopen(path,mode);
    curl_global_init(CURL_GLOBAL_ALL);
    CURLcode res;
    CURL *curl = curl_easy_init();
    curl_easy_setopt(curl,CURLOPT_URL,"http://github.com/fishCoder");
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, write_data);
    curl_easy_setopt(curl, CURLOPT_VERBOSE, 1);
    res = curl_easy_perform(curl);
    curl_easy_cleanup(curl);
    return 0; 
} 

```

***src/CMakeLists.txt***

```
ADD_EXECUTABLE(curltest main.c)

#查找curl库
FIND_PACKAGE(CURL)

IF(CURL_FOUND)
   INCLUDE_DIRECTORIES(${CURL_INCLUDE_DIR})
   TARGET_LINK_LIBRARIES(curltest ${CURL_LIBRARY})
ELSE(CURL_FOUND)
	#没有找到了 手动设置头文件和库
    MESSAGE(STATUS ”CURL library not found”)
    INCLUDE_DIRECTORIES(/usr/include) 
    TARGET_LINK_LIBRARIES(curltest curl)
ENDIF(CURL_FOUND)

```

>p8 [自己编写一个查找模块](./p8-find-my-module)

CMakeLists.txt	

build/	
	
cmake/		

src/

******

***CMakeLists.txt***

```
cmake_minimum_required(VERSION 3.0)
PROJECT(FINDHELLO)
#添加外部库搜索路径
SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
ADD_SUBDIRECTORY(src)
```

***src/CMakeLists.txt***

```
FIND_PACKAGE(HELLO)
IF(HELLO_FOUND)
    ADD_EXECUTABLE(hello main.c)
    INCLUDE_DIRECTORIES(${HELLO_INCLUDE_DIR})
    TARGET_LINK_LIBRARIES(hello ${HELLO_LIBRARY})
ELSE(HELLO_FOUND)
     MESSAGE(FATAL_ERROR "hello library not found")
ENDIF(HELLO_FOUND)

```

***cmake/FindHELLO.cmake***

```
cmake_policy(SET CMP0015 NEW)
#添加头文件搜索路径
FIND_PATH(HELLO_INCLUDE_DIR hello.h ../../p4-library/lib)
#添加库搜索路径
FIND_LIBRARY(HELLO_LIBRARY NAMES hello PATHS ../../p4-library/build/lib)

IF (HELLO_INCLUDE_DIR AND HELLO_LIBRARY)
   SET(HELLO_FOUND TRUE)
ENDIF (HELLO_INCLUDE_DIR AND HELLO_LIBRARY)
IF (HELLO_FOUND)
   IF (NOT HELLO_FIND_QUIETLY)
      MESSAGE(STATUS "Found Hello: ${HELLO_LIBRARY}")
ENDIF (NOT HELLO_FIND_QUIETLY)
ELSE (HELLO_FOUND)
   IF (HELLO_FIND_REQUIRED)
      MESSAGE(FATAL_ERROR "Could not find hello library")
   ENDIF (HELLO_FIND_REQUIRED)
ENDIF (HELLO_FOUND)

```

