cmake_minimum_required (VERSION 2.6)
project (NativeFileDialog)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
find_package(PkgConfig)

if(MSVC OR MSVC80)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /W4")

  set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /MD")
  set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} /O2")

  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} /MDd")
  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} /Z7")
elseif(CMAKE_COMPILER_IS_GNUC OR APPLE)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -pedantic")

  set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3")

  set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -g")
endif()

if(APPLE)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fcolor-diagnostics")
endif()

set(NFD_SOURCE_FILES "nfd_common.c")

if(APPLE)
  list(APPEND NFD_SOURCE_FILES "nfd_cocoa.m")
elseif(MSVC OR MSVC80)
  list(APPEND NFD_SOURCE_FILES "nfd_win.cpp")
elseif(UNIX)
  list(APPEND NFD_SOURCE_FILES "nfd_gtk.c")
  pkg_search_module(GTK REQUIRED gtk+-3.0)
  link_libraries(NFD_SOURCE_FILES ${GTK_LIBRARIES})
  include_directories(NFD_SOURCE_FILES ${GTK_INCLUDE_DIRS})
endif()

include_directories(./include)

add_library(nfd ${NFD_SOURCE_FILES})

install(TARGETS nfd DESTINATION lib)
install(DIRECTORY "include" DESTINATION ".")
install(FILES NativeFileDialog-config.cmake DESTINATION ".")
