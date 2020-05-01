cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(
        HUNTER_CACHE_SERVERS
        "https://github.com/bmstu-iu8-cpp-sem-3/hunter-cache"
        CACHE STRING "Default cache server"
)

include("tools/gate/cmake/HunterGate.cmake")

HunterGate(
        URL "https://github.com/ruslo/hunter/archive/v0.23.214.tar.gz"
        SHA1 "e14bc153a7f16d6a5eeec845fb0283c8fad8c358"
)

project(template)

string(APPEND CMAKE_CXX_FLAGS " -pedantic -Werror -Wall -Wextra")
string(APPEND CMAKE_CXX_FLAGS " -Wno-unused-command-line-argument")
string(APPEND CMAKE_CXX_FLAGS " -Wshadow -Wnon-virtual-dtor")

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)

file(GLOB CPPS "*.cpp" "*.h" "*.c" ".hpp")

add_executable(template ${CPPS})

add_executable(tests
        # TODO: enum your files and delete this comment
        ${CMAKE_CURRENT_SOURCE_DIR}/tests/test.cpp
        )

hunter_add_package(
        Boost
        COMPONENTS
        program_options
        thread
        log
        filesystem
)

find_package (Threads REQUIRED)
find_package(Boost REQUIRED COMPONENTS log)
find_package(Boost REQUIRED COMPONENTS log_setup)
find_package(Boost REQUIRED COMPONENTS program_options)

hunter_add_package(rocksdb)
find_package(RocksDB CONFIG REQUIRED)

target_link_libraries(template
        ${CMAKE_THREAD_LIBS_INIT}
        )


set(Boost_USE_STATIC_LIBS        ON)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME    OFF)
find_package(Boost REQUIRED COMPONENTS system)

target_include_directories(
        template
        PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_libraries(
        template
        Boost::system
        RocksDB::rocksdb
        Boost::log
        Boost::log_setup
        Boost::program_options)

enable_testing()
add_test(NAME unit_tests COMMAND tests)

if(BUILD_COVERAGE)
    set(ENABLE_COVERAGE ON CACHE BOOL "Enable coverage build." FORCE)
    list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
    find_package(codecov)
    # TODO: change template word to project name and delete this comment
    add_coverage(template)
    add_coverage(tests)
    list(APPEND LCOV_REMOVE_PATTERNS "'${PROJECT_SOURCE_DIR}/tests/*'")
    coverage_evaluate()
endif()