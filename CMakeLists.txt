###############################################################################
# project setting
set(PROJECT_NAME MyOpenCL)
set(CMAKE_SUPPRESS_REGENERATION TRUE)
cmake_minimum_required(VERSION 2.8.7)
project( ${PROJECT_NAME} )

###############################################################################
# sub module

if( UNIX )
    message( STATUS "Found project lib ..." )
    add_subdirectory( lib )
endif( )

set( SUBDIRECTORIES 
common
hello_world_opencl
image
)

foreach( subdir ${SUBDIRECTORIES} )
    message( STATUS "Found project ${subdir} ..." )
    add_subdirectory( ${subdir} )
endforeach( subdir )