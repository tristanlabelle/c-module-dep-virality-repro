cmake_minimum_required(VERSION 3.30)
project(Virality LANGUAGES Swift C)

add_library(One INTERFACE)
target_include_directories(One INTERFACE include)

add_library(Two STATIC Two.swift)
target_link_libraries(Two PRIVATE One)

add_library(Three STATIC Three.swift)
target_link_libraries(Three PRIVATE Two)
