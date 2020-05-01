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

project(lab-10-kv-storage)

file(GLOB CPPS "*.cpp" "*.h" "*.c" ".hpp")

add_executable(lab-10-kv-storage ${CPPS})

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

target_link_libraries(lab10
        ${CMAKE_THREAD_LIBS_INIT}
        )


set(Boost_USE_STATIC_LIBS        ON)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME    OFF)
find_package(Boost REQUIRED COMPONENTS system)

target_link_libraries(
        lab-10-kv-storage
        Boost::system
        RocksDB::rocksdb
        Boost::log
        Boost::log_setup
        Boost::program_options)
