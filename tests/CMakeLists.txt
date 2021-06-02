
include(FetchContent)
FetchContent_Declare(doctest
  GIT_REPOSITORY "https://github.com/onqtam/doctest"
  GIT_TAG origin/dev)

find_package(doctest CONFIG QUIET)
if(NOT Doctest_FOUND)
  FetchContent_MakeAvailable(doctest)
endif()

add_executable(tests tests.cpp)
target_link_libraries(tests PRIVATE ml::${PROJECT_NAME})
target_link_libraries(tests PRIVATE doctest::doctest)

add_test(NAME tests COMMAND tests)
