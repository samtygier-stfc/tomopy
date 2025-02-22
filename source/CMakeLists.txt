# ##############################################################################
# # General                                        # #
# ##############################################################################
# ensure PIC flag is set
set(PYTHON_SITE_DIR ${CMAKE_INSTALL_PREFIX}/source)
set(BUILD_STATIC_LIBS
    ON
    CACHE BOOL "Build static libraries")
set(BUILD_SHARED_LIBS
    OFF
    CACHE BOOL "Build shared libraries")
set(PTL_DEVELOPER_INSTALL
    OFF
    CACHE BOOL "Install headers, cmake export, and shared libs" FORCE)
set(CMAKE_POSITION_INDEPENDENT_CODE
    ON
    CACHE BOOL "Position independent code" FORCE)
# ------------------------------------------------------------------------------#
#
# PTL submodule
#
# ------------------------------------------------------------------------------#
checkout_git_submodule(
  RECURSIVE
  TEST_FILE
  CMakeLists.txt
  RELATIVE_PATH
  source/PTL
  WORKING_DIRECTORY
  ${PROJECT_SOURCE_DIR})

if(TOMOPY_USE_PTL)
  add_subdirectory(PTL)
  if(BUILD_STATIC_LIBS)
    list(APPEND TOMOPY_EXTERNAL_LIBRARIES ptl-static)
  else()
    list(APPEND TOMOPY_EXTERNAL_LIBRARIES ptl-shared)
  endif()
  list(APPEND ${PROJECT_NAME}_DEFINITIONS TOMOPY_USE_PTL)
endif()

# ------------------------------------------------------------------------------#
#
# TomoPy Python module
#
# ------------------------------------------------------------------------------#

# where to place the library
set(OUTPUT_PATH ${CMAKE_BINARY_DIR})

add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/libtomo)

# ------------------------------------------------------------------------------#
#
# Code coverage
#
# ------------------------------------------------------------------------------#
if(TOMOPY_USE_COVERAGE)
  foreach(_SRC_FILE ${LIBRARY_SOURCES})
    string(REPLACE "${CMAKE_CURRENT_SOURCE_DIR}" "${CMAKE_CURRENT_BINARY_DIR}"
                   _BIN_FILE "${_SRC_FILE}")
    configure_file(${_SRC_FILE} ${_BIN_FILE} COPYONLY)
  endforeach()
endif()
