AUX_SOURCE_DIRECTORY(. DIR_SRCS)  
add_executable(makekey ${DIR_SRCS})
target_link_libraries(makekey common)