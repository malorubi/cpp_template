
include(FetchContent)
FetchContent_Declare(doctest
  GIT_REPOSITORY "https://github.com/onqtam/doctest"
  GIT_TAG origin/dev)

find_package(doctest CONFIG QUIET)
if(NOT Doctest_FOUND)
  FetchContent_MakeAvailable(doctest)
endif()
include (${doctest_SOURCE_DIR}/scripts/cmake/doctest.cmake)

add_library(testing INTERFACE)
target_compile_definitions(testing INTERFACE DOCTEST_CONFIG_IMPLEMENT_WITH_MAIN)
target_compile_definitions(testing INTERFACE DOCTEST_CONFIG_COLOR_ANSI)
target_link_libraries(testing INTERFACE ml::${PROJECT_NAME})
target_link_libraries(testing INTERFACE doctest::doctest)

add_executable(tests tests.cpp)
target_link_libraries(tests PRIVATE testing)
doctest_discover_tests(tests PROPERTIES TIMEOUT 1)
