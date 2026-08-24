cmake_minimum_required(VERSION 4.3)
project(libstrophe)
enable_testing()
set_property(GLOBAL PROPERTY USE_FOLDERS ON) # enables MSVC Solution Folders
########################################
if(MSVC)
  add_definitions(
    -D_CRT_NONSTDC_NO_DEPRECATE
    -D_CRT_SECURE_NO_WARNINGS
    -D_WINSOCK_DEPRECATED_NO_WARNINGS
    )
endif()
########################################
find_package(libexpat)
if(NOT libexpat_FOUND)
  message(FATAL_ERROR "can't find libexpat - libstrophe cmake currently hard-coded to use libexpat")
endif()
find_package(openssl)
if(NOT openssl_FOUND)
  message(FATAL_ERROR "can't find OpenSSL - libstrophe cmake currently hard-coded to use OpenSSL")
endif()
####################
include(CheckLibraryExists)
function(checkLibraryConcat lib symbol liblist)
  string(TOUPPER ${lib} LIB)
  check_library_exists("${lib}" "${symbol}" "" LIBSTROPHE_HAS_${LIB})
  if(LIBSTROPHE_HAS_${LIB})
    list(APPEND ${liblist} ${lib})
    set(${liblist} ${${liblist}} PARENT_SCOPE)
  endif()
endfunction()
if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
  set(fn __res_query)
else()
  set(fn res_query)
endif()
checkLibraryConcat(resolv ${fn} SYS_LIBS)
checkLibraryConcat(winmm timeGetTime SYS_LIBS)
checkLibraryConcat(ws2_32 getch SYS_LIBS)
########################################
set(targetsFile ${PROJECT_NAME}-targets)
if(NOT DEFINED CMAKE_INSTALL_CMAKEDIR)
  set(CMAKE_INSTALL_CMAKEDIR ${CMAKE_INSTALL_DATADIR}/cmake)
endif()
add_subdirectory(src)
add_subdirectory(examples)
add_subdirectory(tests)
install(FILES strophe.h strophepp.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})
if(COMMAND xpExternPackage)
  xpExternPackage(TARGETS_FILE ${targetsFile}
    LIBRARIES ${PROJECT_NAME} DEFAULT_TARGETS ${PROJECT_NAME}
    BASE 0.9.1 XPDIFF "intro"
    WEB "http://strophe.im/libstrophe/" UPSTREAM "github.com/strophe/libstrophe"
    DESC "A simple, lightweight C library for writing XMPP client"
    LICENSE "[MIT OR GPL-3.0-only](https://github.com/strophe/libstrophe/blob/0.9.1/LICENSE.txt 'dual licensed under MIT and GPLv3 licenses')"
    )
endif()
