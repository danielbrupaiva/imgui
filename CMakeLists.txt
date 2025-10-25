cmake_minimum_required(VERSION 3.22)
include(FetchContent)
#---------------------
if(CMAKE_BINARY_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    message(FATAL_ERROR "-- In-source builds are not allowed.")
endif()
#---------------------
set(PROJECT_NAME imgui)
message("${BoldGreen}-- ${PROJECT_NAME} BUILD ${ColourReset}")
#---------------------
add_library(${PROJECT_NAME} STATIC 
        ${CMAKE_CURRENT_SOURCE_DIR}/imgui.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_draw.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_demo.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_tables.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/imgui_widgets.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/misc/cpp/imgui_stdlib.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
)
#---------------------
target_include_directories(${PROJECT_NAME} PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/backends/
        ${CMAKE_CURRENT_SOURCE_DIR}/misc/
        ${CMAKE_CURRENT_SOURCE_DIR}/misc/cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/misc/fonts
)
#---------------------
# OPEN GL
#---------------------
if (OPENGL3)
    message("${BoldYellow}-- OPENGL3: ${OPENGL3}${ColourReset}")
    find_package(OpenGL REQUIRED)
    target_link_libraries(${PROJECT_NAME} PUBLIC OpenGL::GL)
endif ()
#---------------------
# GLFW
#---------------------
if (GLFW)
    message("${BoldYellow}-- GLFW: ${GLFW}${ColourReset}")
    find_package(glfw3)
    if (NOT glfw3_FOUND)
        message("${BoldYellow}-- GLFW NOT FOUND${ColourReset}")
        message("${BoldYellow}-- BUILDING FROM SRC${ColourReset}")
        set(GLFW_VULKAN_STATIC ON CACHE BOOL "" FORCE)
        set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
        set(GLFW_BUILD_WAYLAND ON CACHE BOOL "" FORCE)
        set(GLFW_BUILD_X11 ON CACHE BOOL "" FORCE)
        set(GLFW_BUILD_KMSDRM ON CACHE BOOL "" FORCE)

        FetchContent_Declare(
                glfw
                GIT_REPOSITORY https://github.com/glfw/glfw.git
                GIT_TAG 3.4
        )
        FetchContent_MakeAvailable(glfw)
        FetchContent_GetProperties(glfw)
    endif ()
    target_link_libraries(${PROJECT_NAME} PUBLIC glfw)
endif ()
