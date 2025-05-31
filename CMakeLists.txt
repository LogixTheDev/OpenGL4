cmake_minimum_required(VERSION 3.30.2)
project(OpenGL4)

add_library(OpenGL4 STATIC ${CMAKE_CURRENT_SOURCE_DIR}/src/gl.c ${CMAKE_CURRENT_SOURCE_DIR}/src/wgl.c)

target_include_directories(OpenGL4 PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)