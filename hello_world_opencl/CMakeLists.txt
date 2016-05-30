###############################################################################
# project setting
set(PROJECT_NAME hello_world_opencl)
set(CMAKE_SUPPRESS_REGENERATION TRUE)
cmake_minimum_required(VERSION 2.8.7)
project( ${PROJECT_NAME} )

###############################################################################
# OpenCL package
#find_package( OpenCL 1.1 REQUIRED )
#message( STATUS "OpenCL_INCLUDE_DIRS : ${OpenCL_INCLUDE_DIRS}" )
#message( STATUS "OpenCL_LIBRARIES : ${OpenCL_LIBRARIES}" )
#message( STATUS "OpenCL_VERSION_STRING : ${OpenCL_VERSION_STRING}" )
#message( STATUS "OpenCL_VERSION_MAJOR : ${OpenCL_VERSION_MAJOR}" )
#message( STATUS "OpenCL_VERSION_MINOR : ${OpenCL_VERSION_MINOR}" )

###############################################################################
# find header path
find_path( OPENCL_INCLUDE_DIRS
	NAMES OpenCL/cl.h CL/cl.h
	HINTS ../include $ENV{AMDAPPSDKROOT}/include
)
mark_as_advanced( OPENCL_INCLUDE_DIRS )
#message( STATUS "OPENCL_INCLUDE_DIRS = " ${OPENCL_INCLUDE_DIRS} )

###############################################################################
# find libary path
if( MSVC )
	set(BITNESS_SUFFIX x86_64)
    	find_library( OPENCL_LIBRARIES
    	NAMES OpenCL
    	HINTS ../lib $ENV{AMDAPPSDKROOT}/lib
    	PATH_SUFFIXES ${BITNESS_SUFFIX}
    )
else( )
	set( OPENCL_LIBRARIES OpenCL )
endif( )
mark_as_advanced( OPENCL_LIBRARIES )
#message( STATUS "OPENCL_LIBRARIES = " ${OPENCL_LIBRARIES} )

if( OPENCL_INCLUDE_DIRS STREQUAL "" OR OPENCL_LIBRARIES STREQUAL "")
	message( FATAL_ERROR "Could not locate OpenCL include & libs" )
endif( )

###############################################################################
# compiler options
set( COMPILER_FLAGS " " )
set( LINKER_FLAGS " " )
if( MSVC )
#    message( STATUS "MSVC compiler options" )
#    add_definitions( "/W3 /D_CRT_SECURE_NO_WARNINGS /wd4005 /wd4996 /nologo" )
    add_definitions( "/W3" )
else( )
    message( STATUS "Should GNU compiler" )
endif( )

###############################################################################
# compiler target
include_directories( include ${OPENCL_INCLUDE_DIRS} ../common/include ../image/include )
file(GLOB SOURCES "src/*.cpp" )
add_executable( ${PROJECT_NAME} ${SOURCES} )
set_target_properties( ${PROJECT_NAME} PROPERTIES 
    COMPILE_FLAGS ${COMPILER_FLAGS}
    LINK_FLAGS ${LINKER_FLAGS} 
)
target_link_libraries( ${PROJECT_NAME} ${OPENCL_LIBRARIES} common image )

###############################################################################
# install
install( TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_BINARY_DIR}/bin )

file(GLOB CL_SOURCES "src/*.cl" )
install(FILES ${CL_SOURCES} DESTINATION ${CMAKE_BINARY_DIR}/bin/assets )