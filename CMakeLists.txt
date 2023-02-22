cmake_minimum_required(VERSION 3.23)
project(hello CXX)

add_library(hello src/hello.cpp)
target_include_directories(hello PUBLIC include)

set_target_properties(hello PROPERTIES PUBLIC_HEADER "include/hello/hello.h")
install(TARGETS hello)
