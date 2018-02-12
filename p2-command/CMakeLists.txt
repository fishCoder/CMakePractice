PROJECT(P2_COMMAND)

#变量赋值
SET(PARAM 'this is param')

#赋多个值 给变量SRC
SET(SRC main.c fun.h fun.c)

MESSAGE(STATUS 'PARAM :' ${PARAM})
MESSAGE(STATUS 'SRC:' ${SRC})

#生成执行文件fun
ADD_EXECUTABLE(fun ${SRC})
