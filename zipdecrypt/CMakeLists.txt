AUX_SOURCE_DIRECTORY(. DIR_SRCS)  
add_executable(zipdecrypt ${DIR_SRCS})
target_link_libraries(zipdecrypt common)