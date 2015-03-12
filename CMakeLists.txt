# Check if cmake has the required version
CMAKE_MINIMUM_REQUIRED(VERSION 2.6.0 FATAL_ERROR)
set(PROJECT_NAME_STR TopHitFit)
PROJECT(${PROJECT_NAME_STR})
set(PROJECT_LIB_NAME TopQuarkAnalysis)

# this project is built as TopQuarkAnalysis/TopHitFit
# need to add the path above to the includes
include_directories(${PROJECT_SOURCE_DIR}/../../)

set(CMAKE_MODULE_PATH
    ${PROJECT_SOURCE_DIR}/cmake/Modules
    ${CMAKE_MODULE_PATH})
    
if((CMAKE_SYSTEM_PROCESSOR MATCHES "i386") AND (CMAKE_SIZEOF_VOID_P EQUAL 8) AND (APPLE))
	set(CMAKE_OSX_ARCHITECTURES "x86_64")
	MESSAGE(STATUS "Building ${PROJECT_NAME_STR} for ${CMAKE_OSX_ARCHITECTURES} architecture on ${CMAKE_SYSTEM_NAME}")
else()
	MESSAGE(STATUS "Building ${PROJECT_NAME_STR} on ${CMAKE_SYSTEM_NAME}")
endif()
MESSAGE(STATUS "Using compiler ${CMAKE_CXX_COMPILER_ID}")
# get CLHEP
include(${PROJECT_SOURCE_DIR}/cmake/Modules/FindCLHEP.cmake)
MESSAGE(STATUS "** CLHEP Include path: ${CLHEP_INCLUDE_DIRS}")
MESSAGE(STATUS "** CLHEP Libraries: ${CLHEP_LIBRARIES}")
include_directories(${CLHEP_INCLUDE_DIRS})
link_directories(${CLHEP_LIBRARIES})

#Check the compiler and set the compile and link flags
set(CMAKE_BUILD_TYPE Release)
set(CMAKE_CXX_FLAGS "-O3 -std=c++11")
# first attempt to make cmake work again on OS X
if((CMAKE_CXX_COMPILER_ID STREQUAL "Clang") AND (APPLE))
	MESSAGE(STATUS "** std library for clang: libc++")
	set(CMAKE_CXX_LINK_FLAGS "${CMAKE_CXX_LINK_FLAGS} -stdlib=libc++")
endif()
file(GLOB_RECURSE SRC_COMMON "src/*.cc")
# Excluding unnecessary files (for stand-alone)
file(GLOB_RECURSE SRC_EXCLUDE "src/Pat*.cc")
list(REMOVE_ITEM SRC_COMMON ${SRC_EXCLUDE})

#adding the library
add_library(${PROJECT_LIB_NAME} SHARED ${SRC_COMMON})
target_link_libraries(${PROJECT_LIB_NAME}
		${CLHEP_LIBRARIES}
        )
