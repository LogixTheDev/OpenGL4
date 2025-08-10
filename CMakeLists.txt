cmake_minimum_required(VERSION 3.30.2)
project(OpenGL4 VERSION 2.0.0 LANGUAGES C)

option(GLAD_BUILD_SHARED "Build glad as a shared library" OFF)

set(GLAD_SOURCES "")
foreach(_cand gl.c wgl.c egl.c glx.c vulkan.c)
    if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/src/${_cand}")
        list(APPEND GLAD_SOURCES "src/${_cand}")
    endif()
endforeach()

if(GLAD_BUILD_SHARED)
    add_library(glad SHARED ${GLAD_SOURCES})
else()
    add_library(glad STATIC ${GLAD_SOURCES})
endif()
add_library(glad::glad ALIAS glad)

target_include_directories(glad
        PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>)

if(WIN32)
    target_link_libraries(glad PUBLIC opengl32)
else()
    target_link_libraries(glad PUBLIC ${CMAKE_DL_LIBS})
endif()

set_target_properties(glad PROPERTIES POSITION_INDEPENDENT_CODE ON)

include(CMakePackageConfigHelpers)

install(TARGETS glad
        EXPORT gladTargets
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include)

install(DIRECTORY include/ DESTINATION include)

write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/gladConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY SameMajorVersion)

file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/gladConfig.cmake"
        "@PACKAGE_INIT@\ninclude(\"\${CMAKE_CURRENT_LIST_DIR}/gladTargets.cmake\")\n")

install(EXPORT gladTargets
        NAMESPACE glad::
        FILE gladTargets.cmake
        DESTINATION lib/cmake/glad)

install(FILES
        "${CMAKE_CURRENT_BINARY_DIR}/gladConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/gladConfigVersion.cmake"
        DESTINATION lib/cmake/glad)

export(PACKAGE glad)