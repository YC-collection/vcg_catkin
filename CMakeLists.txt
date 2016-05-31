cmake_minimum_required(VERSION 2.8.3)
project(vcg_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/vcg)

ExternalProject_Add(vcg_src
                    SVN_REPOSITORY svn://svn.code.sf.net/p/vcg/code/trunk/vcglib
                    CONFIGURE_COMMAND ""
                    UPDATE_COMMAND ""
                    BUILD_COMMAND ${CMAKE_COMMAND} -E copy_directory <SOURCE_DIR>
                    ${CATKIN_DEVEL_PREFIX}/include/vcg
                    INSTALL_COMMAND "")

add_custom_target(copy)           
add_custom_command(TARGET copy PRE_BUILD
				   COMMAND ${CMAKE_COMMAND} -E
				   copy svn://svn.code.sf.net/p/vcg/code/trunk/vcglib/vcg/wrap/ply/plylib.cpp
				   ${CATKIN_DEVEL_PREFIX}/include/vcg/wrap/ply/)
                         
ExternalProject_Get_Property(vcg_src source_dir)
set(VcgIncludeDir ${source_dir}/vcg)

cs_add_library(${PROJECT_NAME} src/dependency-tracker.cpp
                               ${CATKIN_DEVEL_PREFIX}/include/vcg/wrap/ply/plylib.cpp)
                               add_dependencies(${PROJECT_NAME} vcg_src)

cs_install()

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/vcg
                  DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
                  FILES_MATCHING PATTERN "*.hpp"
                  PATTERN "*.h"
                  PATTERN ".svn" EXCLUDE)

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/vcg)