AUX_SOURCE_DIRECTORY(. DIR_SRCS)  
add_executable(pkcrack ${DIR_SRCS})
target_link_libraries(pkcrack common)