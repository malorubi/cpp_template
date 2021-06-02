
include(FetchContent)
FetchContent_Declare(BoostUT
  GIT_REPOSITORY "https://github.com/boost-ext/ut"
  GIT_TAG origin/master)

FetchContent_MakeAvailable(BoostUT)

add_executable(tests tests.cpp)
target_link_libraries(tests PRIVATE ml::${PROJECT_NAME})

add_test(NAME tests COMMAND tests)
