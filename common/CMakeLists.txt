###############################################################################
# project setting
set(PROJECT_NAME common)
set(CMAKE_SUPPRESS_REGENERATION TRUE)
cmake_minimum_required(VERSION 2.8.7)
project( ${PROJECT_NAME} )

###############################################################################
# find header path
find_path( OPENCL_INCLUDE_DIRS
	NAMES OpenCL/cl.h CL/cl.h
	HINTS ../include $ENV{AMDAPPSDKROOT}/include
)
mark_as_advanced( OPENCL_INCLUDE_DIRS )
message( STATUS "OPENCL_INCLUDE_DIRS = " ${OPENCL_INCLUDE_DIRS} )

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
message( STATUS "OPENCL_LIBRARIES = " ${OPENCL_LIBRARIES} )

if( OPENCL_INCLUDE_DIRS STREQUAL "" OR OPENCL_LIBRARIES STREQUAL "")
	message( FATAL_ERROR "Could not locate OpenCL include & libs" )
endif( )

#message( STATUS "CMAKE_SIZEOF_VOID_P = " ${CMAKE_SIZEOF_VOID_P} )
#message( STATUS "BITNESS = " ${BITNESS} )

###############################################################################
# compiler options
set( COMPILER_FLAGS " " )
set( LINKER_FLAGS " " )
if( MSVC )
    message( STATUS "MSVC compiler options" )
#    add_definitions( "/W3 /D_CRT_SECURE_NO_WARNINGS /wd4005 /wd4996 /nologo" )
    add_definitions( "/W3" )
else( )
    message( STATUS "Should GNU compiler" )
endif( )

###############################################################################
# compiler target
include_directories( include ${OPENCL_INCLUDE_DIRS} )
file(GLOB SOURCES "src/*.cpp")
add_library( ${PROJECT_NAME} SHARED ${SOURCES} )
set_target_properties( ${PROJECT_NAME} PROPERTIES 
    COMPILE_FLAGS ${COMPILER_FLAGS}
    LINK_FLAGS ${LINKER_FLAGS} 
)
target_link_libraries( ${PROJECT_NAME} ${OPENCL_LIBRARIES} )

if( MSVC )
include(GenerateExportHeader)
generate_export_header(common)
#GENERATE_EXPORT_HEADER(common
#    BASE_NAME common
#    EXPORT_MACRO_NAME common_EXPORTS
#    EXPORT_FILE_NAME common_exports.h
#    STATIC_DEFINE SHARED_EXPORTS_BUILT_AS_STATIC)
target_include_directories( ${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_BINARY_DIR} )
endif( )
###############################################################################
# install
install( TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_BINARY_DIR}/bin )