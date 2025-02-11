###################################################################################
#
#  Copyright (c) 2014, webvariants GmbH, http://www.webvariants.de
#
#  This file is released under the terms of the MIT license. You can find the
#  complete text in the attached LICENSE file or online at:
#
#  http://www.opensource.org/licenses/mit-license.php
#
#  @author: Tino Rusch (tino.rusch@webvariants.de)
#
###################################################################################

cmake_minimum_required(VERSION 3.20 FATAL_ERROR)

project(bcrypt)

enable_language(ASM)

set(MYLIB_VERSION_MAJOR 1)
set(MYLIB_VERSION_MINOR 0)
set(MYLIB_VERSION_PATCH 0)
set(MYLIB_VERSION_STRING ${MYLIB_VERSION_MAJOR}.${MYLIB_VERSION_MINOR}.${MYLIB_VERSION_PATCH})

# just doing cmake . will build a shared or static lib and honor existing environment setting
# to force build static, cmake . -DBUILD_SHARED_LIBS=Off
# to force build shared, cmake . -DBUILD_SHARED_LIBS=On

if (NOT BUILD_SHARED_LIBS)
    message ("Building a static library")
else ()
    message ("Building a shared library")
endif ()


set( CMAKE_COLOR_MAKEFILE ON )

set( CMAKE_CXX_FLAGS  "${CMAKE_CXX_FLAGS} -Wall --std=c++11 -O3" )
set( CMAKE_C_FLAGS  "${CMAKE_C_FLAGS} -Wall -O3" )

set( CMAKE_ASM_FLAGS "${CXXFLAGS} -x assembler-with-cpp")

set( SRCFILES
        ${CMAKE_CURRENT_SOURCE_DIR}/src/bcrypt.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/crypt_blowfish.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/crypt_gensalt.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/wrapper.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/x86.S
        )

if (NOT WIN32)
    include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/include/bcrypt)
endif()

include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/include)

add_library(
        ${PROJECT_NAME}
        ${SRCFILES}
)

set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${MYLIB_VERSION_STRING} SOVERSION ${MYLIB_VERSION_MAJOR})

set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER include/bcrypt/BCrypt.hpp)

target_include_directories(${PROJECT_NAME} PRIVATE include)
target_include_directories(${PROJECT_NAME} PRIVATE src)
