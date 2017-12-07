cmake_minimum_required (VERSION 2.6)
project(PKCrack) 

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

include_directories(include)

add_subdirectory(common)
add_subdirectory(pkcrack)
add_subdirectory(findkey)
add_subdirectory(zipdecrypt)
add_subdirectory(extract)
add_subdirectory(makekey)