cmake_minimum_required(VERSION 3.28)

project(
    ccld_ToggleUI
    VERSION 4.7.0
    DESCRIPTION "SKSE plugin that allows users to toggle UI."
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_INTERPROCEDURAL_OPTIMIZATION "$<$<CONFIG:Release>:ON>")

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/version.rc.in"
    "${CMAKE_CURRENT_BINARY_DIR}/version.rc"
    @ONLY
)

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/ccld_ToggleUI.toml"
    "${CMAKE_CURRENT_BINARY_DIR}/ccld_ToggleUI.toml"
    COPYONLY
)

# -- Declare Sources -----------------------------------------------------------

include(cmake/headerlist.cmake)
include(cmake/sourcelist.cmake)

source_group(
    TREE
        "${CMAKE_CURRENT_SOURCE_DIR}"
    FILES
        ${PROJECT_HEADERS}
        ${PROJECT_SOURCES}
)

# -- Declare Dependencies ------------------------------------------------------

if(NOT CMAKE_PREFIX_PATH AND DEFINED ENV{DEV_INSTALL_PATH})
    cmake_path(SET CMAKE_PREFIX_PATH "$ENV{DEV_INSTALL_PATH}/${CMAKE_BUILD_TYPE}")
endif()

find_package(CommonLibSSE REQUIRED)
find_package(fmt REQUIRED)
find_package(spdlog REQUIRED)
find_package(tomlplusplus REQUIRED)

# -- Declare Targets -----------------------------------------------------------

add_commonlibsse_plugin(
    "${PROJECT_NAME}"
    SOURCES
        ${PROJECT_HEADERS}
        ${PROJECT_SOURCES}
        "${CMAKE_CURRENT_BINARY_DIR}/version.rc"
)

target_compile_definitions(
    "${PROJECT_NAME}"
    PRIVATE
        _UNICODE
        NOMINMAX
)

target_compile_features(
    "${PROJECT_NAME}"
    PRIVATE
        cxx_std_23
)

if(MSVC)
    target_compile_options(
        "${PROJECT_NAME}"
        PRIVATE
            /EHsc
            /permissive-
            /sdl
            /utf-8
            /W4
            /Zc:__cplusplus
            /Zc:preprocessor
    )
endif()

target_include_directories(
    "${PROJECT_NAME}"
    PRIVATE
        "${CMAKE_CURRENT_SOURCE_DIR}/src"
)

target_link_libraries(
    "${PROJECT_NAME}"
    PRIVATE
        fmt::fmt
        spdlog::spdlog
        tomlplusplus::tomlplusplus
)

target_precompile_headers(
    "${PROJECT_NAME}"
    PRIVATE
        "src/PCH.h"
)
