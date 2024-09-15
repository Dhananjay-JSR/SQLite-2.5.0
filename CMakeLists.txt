cmake_minimum_required(VERSION 3.15)
project(SQLITE_Cmake)
set(CMAKE_C_STANDARD 11)


file(READ "${CMAKE_SOURCE_DIR}/VERSION" PROJECT_VERSION)
string(STRIP "${PROJECT_VERSION}" PROJECT_VERSION)

# This is the format DB Encode Text Format in Either UTF8 or ISO8859
set(ENCODING "ISO8859")

configure_file(
        "${CMAKE_SOURCE_DIR}/src/sqlite.h.in"
        "${CMAKE_BINARY_DIR}/generated/sqlite.h"
        @ONLY
)


# Add custom command to build LEMON
add_custom_command(
        OUTPUT ${CMAKE_BINARY_DIR}/lemon
        COMMAND ${CMAKE_C_COMPILER} -o lemon ${CMAKE_SOURCE_DIR}/tool/lemon.c
        COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/tool/lempar.c ${CMAKE_BINARY_DIR}/lempar.c
        DEPENDS ${CMAKE_SOURCE_DIR}/tool/lemon.c ${CMAKE_SOURCE_DIR}/tool/lempar.c
        COMMENT "Building LEMON compiler generator"
)

# Lemon Parser , This Ensures that Lemon is Build from Previos ^ Custom Command
add_custom_target(lemon_target ALL DEPENDS ${CMAKE_BINARY_DIR}/lemon)


add_custom_command(
        OUTPUT ${CMAKE_BINARY_DIR}/parse.c ${CMAKE_BINARY_DIR}/parse.h
        COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_SOURCE_DIR}/src/parse.y ${CMAKE_BINARY_DIR}/parse.y
        COMMAND ${CMAKE_BINARY_DIR}/lemon ${CMAKE_BINARY_DIR}/parse.y
        DEPENDS ${CMAKE_SOURCE_DIR}/src/parse.y ${CMAKE_BINARY_DIR}/lemon
        COMMENT "Generating parse.c and parse.h"
)

add_custom_target(parser_gen ALL DEPENDS ${CMAKE_BINARY_DIR}/parse.c ${CMAKE_BINARY_DIR}/parse.h)


file(GLOB SOURCES "src/*.c")

#Include Parse from  Builder
list(APPEND SOURCES ${CMAKE_BINARY_DIR}/parse.c)


include_directories(src ${CMAKE_BINARY_DIR} ${CMAKE_BINARY_DIR}/generated)

add_executable(${PROJECT_NAME} ${SOURCES})

# Maintain Dependency Cycle for Pre-Build
# Left Most Depends on Right
add_dependencies(parser_gen lemon_target)

add_dependencies(${PROJECT_NAME} parser_gen)

target_compile_definitions(${PROJECT_NAME} PRIVATE NO_TCL=1)