#
# @file        datalog_api.c
# @author      Alexander Hoffman
# @email       alxhoff@gmail.com
# @website     http://alexhoffman.info
# @copyright   GNU GPL v3
# @brief       API functions to use the LUA/C datalog implementation found
#              found at http://datalog.sourceforge.net
#
#@verbatim
#   ----------------------------------------------------------------------
#    Copyright (C) Alexander Hoffman, 2017
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    any later version.
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#    You should have received a copy of the GNU General Public License
#    along with this program.  If not, see <http://www.gnu.org/licenses/>.
#   ----------------------------------------------------------------------
#@endverbatim
#

project(OPCUA_XML_Generator)
cmake_minimum_required (VERSION 3.4 FATAL_ERROR)

#debug
set(CMAKE_BUILD_TYPE Debug)

#exec output dir
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../bin)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 ")

#Versioning
set(DATALOGAPI_VERSION_MAJOR 1)
set(DATALOGAPI_VERSION_MINOR 0)

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR
    "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
        set(warnings "-Wall -Wextra -Werror")
endif()

#YCM requirements
option(USE_YCM "Generate compiler json for YouCompleteMe" ON)
if (USE_YCM)
    set(CMAKE_EXPORT_COMPILE_COMMANDS 1)
endif (USE_YCM)

#includes
include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/libs/libxml2/include/libxml2)

#SRCS
file(GLOB SOURCES "main.c")

#datalog opcua generator
file(GLOB DATALOG_OPCUA_SRCS "datalog_opcua_generator.c")
add_library(DATALOG_OPCUA SHARED ${DATALOG_OPCUA_SRCS})

#EXECUTABLES
add_executable(EXAMPLE ${SOURCES})

#LINKING
target_link_libraries(EXAMPLE DATALOG_OPCUA)
target_link_libraries(EXAMPLE ${PROJECT_SOURCE_DIR}/libs/libxml2/lib/libxml2.so.2.9.7)
