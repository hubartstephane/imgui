##############################################
# This file is made by shubart for chaos
##############################################

cmake_minimum_required(VERSION 3.20)
project(ImGuiLibrary)

# Options
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# make ImWchar a 32 bits value
add_compile_definitions(IMGUI_USE_WCHAR32)

# user variables
set(GLFW_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../../glfw/${CHAOS_BUILD_TYPE}" CACHE PATH "Path to the GLFW installation")

# check GLFW path
if(NOT EXISTS "${GLFW_PATH}/include" OR NOT EXISTS "${GLFW_PATH}/lib")
    message(FATAL_ERROR "GLFW_PATH is invalid or does not contain the required include/lib directories")
endif()

# add ImGui files
set(IMGUI_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_draw.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_tables.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_widgets.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_demo.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp
)

set(IMGUI_HEADERS
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imconfig.h
    ${CMAKE_CURRENT_SOURCE_DIR}/imgui_internal.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_glfw.h
    ${CMAKE_CURRENT_SOURCE_DIR}/backends/imgui_impl_opengl3.h
)

# add files to static library
add_library(ImGui STATIC ${IMGUI_SOURCES} ${IMGUI_HEADERS})

# include necessary directories
target_include_directories(ImGui PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/backends
    ${GLFW_PATH}/include
)

# link with opengl and GLFW
find_package(OpenGL REQUIRED)

target_link_libraries(ImGui PUBLIC
    ${GLFW_PATH}/lib/glfw3.lib
    OpenGL::GL
)

# install static library
install(TARGETS ImGui
    ARCHIVE DESTINATION lib  # Pour la bibliothèque statique
)

# install headers
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
    DESTINATION include/imgui
    FILES_MATCHING PATTERN "*.h"
)

# install backends (GLFW, OpenGL)
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/backends/
    DESTINATION include/imgui/backends
    FILES_MATCHING PATTERN "*.h"
)

# Message d'info
message(STATUS "Dear ImGui configured with GLFW at ${GLFW_PATH}")
