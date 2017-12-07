AUX_SOURCE_DIRECTORY(. DIR_SRCS)  
add_executable(extract ${DIR_SRCS})
target_link_libraries(extract common)