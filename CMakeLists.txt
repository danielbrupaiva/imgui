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
    find_package(glfw3 REQUIRED)
    target_link_libraries(${PROJECT_NAME} PUBLIC glfw)
endif ()

#---------------------
# STB Image
#---------------------
find_package(stb REQUIRED)
target_link_libraries(${PROJECT_NAME} PUBLIC stb::stb)
