###############################################################################
# project setting
set(PROJECT_NAME OpenCL)
set(CMAKE_SUPPRESS_REGENERATION TRUE)
cmake_minimum_required(VERSION 2.8.7)
project( ${PROJECT_NAME} )

###############################################################################
# compiler options
set( COMPILER_FLAGS " " )
set( LINKER_FLAGS " " )

###############################################################################
# compiler target
include_directories( ../include )
file(GLOB SOURCES "src/*.cpp")
add_library( ${PROJECT_NAME} SHARED opencl_stubs.c )
set_target_properties( ${PROJECT_NAME} PROPERTIES 
    COMPILE_FLAGS ${COMPILER_FLAGS}
    LINK_FLAGS ${LINKER_FLAGS} 
)