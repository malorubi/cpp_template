cmake_minimum_required(VERSION 3.16.0)
project(cpp_template
  VERSION 0.1.0
  DESCRIPTION "cpp_template"
  HOMEPAGE_URL "https://github.com/malorubi/cpp_template"
  LANGUAGES CXX)

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)
include(FetchContent)

file(GLOB_RECURSE documented_headers CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/include/ml/*.hpp)

# --- Options

set (MAIN_PROJECT OFF)
if (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(MAIN_PROJECT ON)
endif()

# --- Library

add_library(${PROJECT_NAME} INTERFACE)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_20)
target_include_directories(${PROJECT_NAME} INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>)
target_include_directories(${PROJECT_NAME} SYSTEM INTERFACE $<INSTALL_INTERFACE:$<INSTALL_PREFIX>/include/>)
add_library(ml::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

# --- Tests

option (ML_CPP_TEMPLATE_ENABLE_TESTS "Compile tests." ${MAIN_PROJECT})
if (ML_CPP_TEMPLATE_ENABLE_TESTS)
  enable_testing()
  add_subdirectory(tests)
endif()

# --- Package
write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake"
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY AnyNewerVersion)

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/cmake/config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

# --- Install

install(TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}_Targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}/
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}/
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ml
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(EXPORT ${PROJECT_NAME}_Targets
  FILE ${PROJECT_NAME}-targets.cmake
  NAMESPACE ml::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

# --- Exports

export(EXPORT ${PROJECT_NAME}_Targets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}/${PROJECT_NAME}-targets.cmake"
  NAMESPACE ml::)

# --- Documentation

option(ML_CPP_TEMPLATE_ENABLE_DOCS "Generate documentation." OFF)
if(ML_CPP_TEMPLATE_ENABLE_DOCS)
  string(REPLACE ";" " " DOXYGEN_INPUT "${documented_headers}")
  add_subdirectory(docs)
endif()