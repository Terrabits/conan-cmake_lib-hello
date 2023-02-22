cmake_minimum_required(VERSION 3.23)
project(PackageTest CXX)

# build/Release/generators/conan_toolchain.cmake

find_package(hello CONFIG REQUIRED)

add_executable(example src/example.cpp)
target_link_libraries(example hello::hello)
