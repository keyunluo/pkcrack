AUX_SOURCE_DIRECTORY(. DIR_SRCS)  
add_executable(findkey ${DIR_SRCS})
target_link_libraries(findkey common)