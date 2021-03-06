cmake_minimum_required(VERSION 3.0)
project(doxybook2)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()

option(USE_CLANG_TIDY "Use clang-tidy" OFF)
option(DOXYBOOK_TESTS "Build Doxybook2 tests" OFF)
if(APPLE OR MSVC)
  set(DOXYBOOK_STATIC_STDLIB OFF)
else()
  option(DOXYBOOK_STATIC_STDLIB "Use static stdlib" ON)
endif()

set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Select build type")
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "RelWithDebInfo" "MinSizeRel")

# Clang tidy
if(USE_CLANG_TIDY)
  find_program(CLANG_TIDY_EXE NAMES "clang-tidy" DOC "Path to clang-tidy executable")
  if(NOT CLANG_TIDY_EXE)
    message(FATAL_ERROR "clang-tidy not found.")
  else()
    message(STATUS "clang-tidy found: ${CLANG_TIDY_EXE}")
  endif()
endif()

# Libraries
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/libs/tinyxml2 EXCLUDE_FROM_ALL)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/libs/fmt EXCLUDE_FROM_ALL)
target_include_directories(fmt INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/fmt/include)
add_library(argagg INTERFACE)
target_include_directories(argagg INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/argagg/include)
add_library(termcolor INTERFACE)
target_include_directories(termcolor INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/termcolor/include)
add_library(json INTERFACE)
target_include_directories(json INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/json/include)
add_library(inja INTERFACE)
target_include_directories(inja INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/inja/include)
add_library(Catch2 INTERFACE)
target_include_directories(Catch2 INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/Catch2/single_include)
if (MSVC)
  add_library(dirent INTERFACE)
  target_include_directories(dirent INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/libs/dirent/include)
endif()

add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/src/Doxybook)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/src/DoxybookCli)
if(DOXYBOOK_TESTS)
  enable_testing()
  add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/tests/DoxybookTests)
endif()
