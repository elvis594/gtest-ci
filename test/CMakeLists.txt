
add_subdirectory(${PROJECT_SOURCE_DIR}/third_parthy/googletest ${PROJECT_BINARY_DIR}/googletest_build)
include(GoogleTest)

# 包含项目的头文件目录
include_directories(${CMAKE_SOURCE_DIR}/include)

# 添加测试可执行文件
add_executable(test_add test_add.cpp)
# 链接gtest和你的库
target_link_libraries(test_add ${GTEST_LIBRARIES} pthread add gtest)

gtest_discover_tests(test_add)

add_executable(test_hello test_hello.cpp)
# 链接gtest和你的库
target_link_libraries(test_hello ${GTEST_LIBRARIES} pthread add gtest)
gtest_discover_tests(test_hello)


if (ENABLE_LCOV)
    message(STATUS "Enable Lcov in ${SUBPRJ}")
    message(STATUS "-- lcov Configure")
    find_program(LCOV_BIN lcov)
    if (NOT LCOV_BIN MATCHES "lcov$")
        message(FATAL_ERROR "lcov required, but not found!")
    endif ()
    if (CMAKE_BUILD_TYPE MATCHES Debug)
        include(CodeCoverage.cmake)
        APPEND_COVERAGE_COMPILER_FLAGS()
        setup_target_for_coverage_lcov(
                NAME coverage
                EXECUTABLE ctest test
                BASE_DIRECTORY "${PROJECT_SOURCE_DIR}/build"
                EXCLUDE "${PROJECT_SOURCE_DIR}/build/_deps/*" "/usr/*"
                DEPENDENCIES test_hello test_add )
    endif ()
endif ()