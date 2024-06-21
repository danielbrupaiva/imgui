cmake_minimum_required(VERSION 3.22)
include(FetchContent)
#---------------------
if(CMAKE_BINARY_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    message(FATAL_ERROR "-- In-source builds are not allowed.")
endif()
#---------------------
set(PROJECT_NAME imgui)
project(${PROJECT_NAME} C CXX)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -static")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS}")
message("${BoldGreen}-- ${PROJECT_NAME} BUILD ${ColourReset}")
#---------------------
set(SOURCES
        imgui.cpp
        imgui_draw.cpp
        imgui_demo.cpp
        imgui_tables.cpp
        imgui_widgets.cpp
        misc/cpp/imgui_stdlib.cpp
        backends/imgui_impl_opengl3.cpp
        backends/imgui_impl_vulkan.cpp
        backends/imgui_impl_glfw.cpp
)
#---------------------
add_library(${PROJECT_NAME} STATIC ${SOURCES})
#---------------------
target_include_directories(${PROJECT_NAME} PUBLIC
        .
        backends/
        misc/cpp
)
include(GNUInstallDirs)
#---------------------
# PKG CONFIG
#---------------------
find_package(PkgConfig REQUIRED)
#---------------------
# OPEN GL
#---------------------
if(OPENGL3)
    message("${BoldYellow}-- OPENGL3: ${OPENGL3}${ColourReset}")
    if(CROSS_COMPILER)
        message("${BoldYellow}-- CROSS_COMPILER: ${CROSS_COMPILER}${ColourReset}")

        set(GL_INC_DIR ${CMAKE_SYSROOT}/usr/include)
        set(GL_LIB_DIR ${CMAKE_SYSROOT}:${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/:${CMAKE_SYSROOT}/usr:${CMAKE_SYSROOT}/usr/lib)

        set(OPENGL_INCLUDE_DIR ${GL_INC_DIR})
        set(OPENGL_opengl_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libOpenGL.so)
        set(OPENGL_glx_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libGLX.so)

        set(EGL_INCLUDE_DIR ${GL_INC_DIR})
        set(EGL_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libEGL.so)

        set(GLESv2_INCLUDE_DIR ${GL_INC_DIR})
        set(GLIB_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libGLESv2.so)

        set(GLESv2_INCLUDE_DIR ${GL_INC_DIR})
        set(GLESv2_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libGLESv2.so)

        set(Libdrm_INCLUDE_DIR ${GL_INC_DIR})
        set(Libdrm_LIBRARY ${CMAKE_SYSROOT}/usr/lib/${TARGET_ARCHITECTURE}/libdrm.so)

        set(gbm_INCLUDE_DIR ${GL_INC_DIR})
        set(gbm_LIBRARY ${CMAKE_SYSROOT}/usr/lib/libgbm.so)
    endif ()

    find_package(OpenGL REQUIRED)
    target_link_libraries(${PROJECT_NAME} PUBLIC OpenGL::GL)
endif()
#---------------------
# Vulkan
#---------------------
if(VULKAN)
    message("${BoldYellow}-- VULKAN: ${VULKAN}${ColourReset}")
    if(CROSS_COMPILER)
        message("${BoldYellow}-- CROSS_COMPILER: ${CROSS_COMPILER}${ColourReset}")
    endif ()
    find_package(VulkanHeaders REQUIRED CONFIG)
    message(STATUS "Vulkan Headers Version: ${VulkanHeaders_VERSION}")
    target_link_libraries(${PROJECT_NAME} PUBLIC Vulkan::Headers)
    find_package(Vulkan REQUIRED)
    target_link_libraries(${PROJECT_NAME} PUBLIC Vulkan::Vulkan)
endif()
#---------------------
# GLFW
#---------------------
if(GLFW)
    message("${BoldYellow}-- GLFW: ${GLFW}${ColourReset}")
    if(NOT BUILD_FROM_SRC)
        find_package(glfw3 REQUIRED)
    elseif( NOT glfw3_FOUND OR BUILD_FROM_SRC )
        message("${BoldYellow}-- GLFW NOT FOUND${ColourReset}")
        message("${BoldYellow}-- BUILDING FROM SRC${ColourReset}")
        set(GLFW_VULKAN_STATIC ON CACHE BOOL "" FORCE)
        set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_WAYLAND ON CACHE BOOL "" FORCE)

        FetchContent_Declare(
                glfw
                GIT_REPOSITORY https://github.com/glfw/glfw.git
                GIT_TAG 3.4
        )
        FetchContent_MakeAvailable(glfw)
        FetchContent_GetProperties(glfw)
    endif ()
    target_link_libraries(${PROJECT_NAME} PUBLIC glfw)
endif()
