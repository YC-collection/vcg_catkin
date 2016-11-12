cmake_minimum_required(VERSION 2.8.3)
project(vcg_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)

set(${PROJECT_NAME}_SOURCE_FILES ${CATKIN_DEVEL_PREFIX}/include/vcg)
file(MAKE_DIRECTORY ${${PROJECT_NAME}_SOURCE_FILES})

ExternalProject_Add(vcg_src
                    URL https://github.com/ethz-asl/vcglib/archive/v1.0.0.zip
                    CONFIGURE_COMMAND ""
                    UPDATE_COMMAND ""
                    BUILD_COMMAND ${CMAKE_COMMAND} -E copy_directory <SOURCE_DIR> ${${PROJECT_NAME}_SOURCE_FILES}
                    INSTALL_COMMAND "")

set_source_files_properties(${${PROJECT_NAME}_SOURCE_FILES}/wrap/ply/plylib.cpp PROPERTIES GENERATED TRUE)
cs_add_library(${PROJECT_NAME} src/dependency-tracker.cpp
                               ${${PROJECT_NAME}_SOURCE_FILES}/wrap/ply/plylib.cpp)
add_dependencies(${PROJECT_NAME} vcg_src)

cs_install()

install(DIRECTORY ${${PROJECT_NAME}_SOURCE_FILES}
                  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
                  FILES_MATCHING PATTERN "*.hpp"
                  PATTERN "*.h"
                  PATTERN ".svn" EXCLUDE)

cs_export(INCLUDE_DIRS ${${PROJECT_NAME}_SOURCE_FILES})
