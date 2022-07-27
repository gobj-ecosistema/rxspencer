# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# Please note that the package source code is licensed under its own license.

project ( regex C )
cmake_minimum_required ( VERSION 3.0 )

set(CMAKE_INSTALL_PREFIX /yuneta/development/output)

option ( BUILD_TESTING "build tests" ON )
option ( INSTALL_DOCS "install documentation" ON )

# Define POSIX_MISTAKE to allow unmatched right parentheses as literals, as
# required by POSIX 1003.2.
# Define REDEBUG to enable debugging.
add_definitions(-DPOSIX_MISTAKE -DREDEBUG)
#add_compile_options(-Wall -Wextra -pedantic -Wconversion)

# regex library
set ( CMAKE_INCLUDE_CURRENT_DIR 1 )
set ( RXSPENCER_SRCS regcomp.c regerror.c regexec.c regfree.c )
set ( RXSPENCER_INCLUDE_DIR include/regex )
set ( RXSPENCER_BIN_DIR bin )
set ( RXSPENCER_LIB_DIR lib )
set ( RXSPENCER_DATA_DIR share/regex )

if ( MSVC )
  set ( DEF_FILE librxspencer.def )
endif ( )

add_library ( regex STATIC ${RXSPENCER_SRCS} ${DEF_FILE} )
add_library ( regexs SHARED ${RXSPENCER_SRCS} ${DEF_FILE} )

set_target_properties ( regex PROPERTIES PUBLIC_HEADER regex.h VERSION 0.0.3 SOVERSION 0)
set_target_properties ( regexs PROPERTIES PUBLIC_HEADER regex.h VERSION 0.0.3 SOVERSION 0 OUTPUT_NAME regex)

target_include_directories ( regex PUBLIC
  $<INSTALL_INTERFACE:${RXSPENCER_INCLUDE_DIR}>
  $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}> )

target_include_directories ( regexs PUBLIC
        $<INSTALL_INTERFACE:${RXSPENCER_INCLUDE_DIR}>
        $<BUILD_INTERFACE:${CMAKE_BINARY_DIR}> )

install ( TARGETS regex
  RUNTIME DESTINATION ${RXSPENCER_BIN_DIR}
  LIBRARY DESTINATION ${RXSPENCER_LIB_DIR}
  ARCHIVE DESTINATION ${RXSPENCER_LIB_DIR}
  PUBLIC_HEADER DESTINATION ${RXSPENCER_INCLUDE_DIR} )

install ( TARGETS regexs
        RUNTIME DESTINATION ${RXSPENCER_BIN_DIR}
        LIBRARY DESTINATION ${RXSPENCER_LIB_DIR}
        ARCHIVE DESTINATION ${RXSPENCER_LIB_DIR}
        PUBLIC_HEADER DESTINATION ${RXSPENCER_INCLUDE_DIR} )

# Install docs
if ( INSTALL_DOCS )
  set ( RXSPENCER_MAN3_DIR man/man3 )
  set ( RXSPENCER_MAN7_DIR man/man7 )
  set ( RXSPENCER_DATA_FILES "COPYRIGHT;README;WHATSNEW" )

  install ( FILES ${RXSPENCER_DATA_FILES} DESTINATION ${RXSPENCER_DATA_DIR} )
  install ( FILES regex.3 DESTINATION ${RXSPENCER_MAN3_DIR} )
  install ( FILES regex.7 DESTINATION ${RXSPENCER_MAN7_DIR} )
endif ( )

if ( BUILD_TESTING )
  enable_testing()
  add_executable(tester ${RXSPENCER_SRCS} debug.c main.c split.c)
  add_test(NAME test1 COMMAND sh -c "./tester -f ${CMAKE_CURRENT_SOURCE_DIR}/tests")
  add_test(NAME test2 COMMAND sh -c "./tester -el -f ${CMAKE_CURRENT_SOURCE_DIR}/tests")
  add_test(NAME test3 COMMAND sh -c "./tester -er -f ${CMAKE_CURRENT_SOURCE_DIR}/tests")
endif ( )
