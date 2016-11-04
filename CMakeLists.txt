#  Sharedlib++
#  Copyright: (C) 2015 Ali Paikan
#  Authors: Ali Paikan <ali.paikan@gmail.com>
#
#  Copy Policy: Released under the terms of the LGPLv2.1 or later, see LGPL.TXT
#

cmake_minimum_required(VERSION 2.8.12)
if(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 OLD)
    cmake_policy(SET CMP0005 NEW)
    cmake_policy(SET CMP0017 NEW)
endif(COMMAND cmake_policy)

include(ExternalProject)

set(PROJECTNAME sharedlibpp)
project(${PROJECTNAME})


# Create the library 
file(GLOB folder_source ./src/*.cpp)
file(GLOB_RECURSE folder_header ./include/*.h)
source_group("Source Files" FILES ${folder_source})
source_group("Header Files" FILES ${folder_header})

include_directories(./include)
add_library(${PROJECTNAME} SHARED ${folder_source} ${folder_header} )
target_link_libraries(${PROJECTNAME})

########################################################################

set(VARS_PREFIX ${PROJECTNAME})

set(${PROJECTNAME}_MAJOR_VERSION 0)
set(${PROJECTNAME}_MINOR_VERSION 0)
set(${PROJECTNAME}_PATCH_VERSION 1)
set(${PROJECTNAME}_VERSION ${${PROJECTNAME}_MAJOR_VERSION}.${${PROJECTNAME}_MINOR_VERSION}.${${PROJECTNAME}_PATCH_VERSION})

find_package(YCM REQUIRED)
include(YCMDefaultDirs)
ycm_default_dirs(${VARS_PREFIX})

target_include_directories(${PROJECTNAME} PUBLIC "$<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/src>"
                                          "$<INSTALL_INTERFACE:${${VARS_PREFIX}_INSTALL_INCLUDEDIR}>")

set_target_properties(${PROJECTNAME} PROPERTIES VERSION ${${VARS_PREFIX}_VERSION}
                              SOVERSION ${${VARS_PREFIX}_VERSION})

install(DIRECTORY include/
        DESTINATION "${${VARS_PREFIX}_INSTALL_INCLUDEDIR}"
        FILES_MATCHING PATTERN "*.h")

install(TARGETS ${PROJECTNAME}
        EXPORT ${PROJECTNAME}
        ARCHIVE DESTINATION "${${VARS_PREFIX}_INSTALL_BINDIR}" COMPONENT lib
        RUNTIME DESTINATION "${${VARS_PREFIX}_INSTALL_BINDIR}" COMPONENT bin
        LIBRARY DESTINATION "${${VARS_PREFIX}_INSTALL_LIBDIR}" COMPONENT shlib)



#enabling it will add all ${PROJECTNAME} dependencies as dependencies for third party users
set_property(GLOBAL APPEND PROPERTY ${VARS_PREFIX}_TARGETS ${PROJECTNAME})


include(InstallBasicPackageFiles)
install_basic_package_files(${PROJECTNAME} VARS_PREFIX ${VARS_PREFIX}
                                    VERSION ${${VARS_PREFIX}_VERSION}
                                    COMPATIBILITY SameMajorVersion
                                    TARGETS_PROPERTY ${VARS_PREFIX}_TARGETS
                                    NO_CHECK_REQUIRED_COMPONENTS_MACRO)

include(AddUninstallTarget)
        

# Create examples
option (BUILD_EXAMPLES "Build examples" ON)
if (BUILD_EXAMPLES)
    add_subdirectory(examples)
endif(BUILD_EXAMPLES)

