cmake_minimum_required(VERSION 2.8.9)

project(CarreraSlice)

#-----------------------------------------------------------------------------
set(KSLICE_VERSION_MAJOR "1")
set(KSLICE_VERSION_MINOR "0")
set(KSLICE_VERSION ${KSLICE_VERSION_MAJOR}.${KSLICE_VERSION_MINOR})

#-----------------------------------------------------------------------------
set(EXTENSION_NAME CarreraSlice)
set(EXTENSION_STATUS "Beta")
set(EXTENSION_DEPENDS "NA") 
set(EXTENSION_SCREENSHOTURLS   "http://www.slicer.org/slicerWiki/images/thumb/2/2c/DrawLabel.png/500px-DrawLabel.png  http://www.na-mic.org/Wiki/images/thumb/d/d0/TendonSegment.png/800px-TendonSegment.png  http://www.na-mic.org/Wiki/images/thumb/b/b8/FiducialSnapshotX.png/800px-FiducialSnapshotX.png")
set(EXTENSION_HOMEPAGE     http://wiki.slicer.org/slicerWiki/index.php/Documentation/Nightly/Modules/CarreraSliceInteractiveSegmenter)
set(EXTENSION_CATEGORY    "Segmentation")
set(EXTENSION_ICONURL    http://wiki.slicer.org/slicerWiki/images/2/2a/CarreraSliceEffect.png)
set(EXTENSION_CONTRIBUTORS  "Ivan Kolesov, Liangjia Zhu, Allen Tannenbaum (Stony Brook University), Peter Karasev, Patricio Vela (GT), Yi Gao (UAB), Karl Fritscher (BWH & MGH), Steve Pieper (Isomics)")
set(EXTENSION_DESCRIPTION   "This extension provides an interactive 3D segmentation tool.")

#-----------------------------------------------------------------------------
set(KSlice_Build_SlicerWrap TRUE CACHE BOOL TRUE)
if(KSlice_Build_SlicerWrap)
  find_package(Slicer REQUIRED)
  include(${Slicer_USE_FILE})
endif()

find_package(VTK REQUIRED)
include(${VTK_USE_FILE})

#-----------------------------------------------------------------------------
# Set a default build type if none was specified
#-----------------------------------------------------------------------------
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to 'Debug' as none was specified.")
  set(CMAKE_BUILD_TYPE Debug CACHE STRING "Choose the type of build." FORCE)
  mark_as_advanced(CMAKE_BUILD_TYPE)
  # Set the possible values of build type for cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release"
    "MinSizeRel" "RelWithDebInfo")
endif()

#-----------------------------------------------------------------------------
include(CTest)

SET(BUILD_SHARED_LIBS ON CACHE INTERNAL ON)
set(CMAKE_VERBOSE_MAKEFILE CACHE bool true FORCE)

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin CACHE PATH "Single Directory for all Executables")
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib CACHE PATH "Single Directory for all Libraries")


if(WIN32)
  set(CMAKE_CXX_FLAGS_DEBUG "/D_DEBUG /MDd /Zi /Ob0 /Od /RTC1" CACHE STRING "extra compiler flags for debug" FORCE )
  set(CMAKE_C_FLAGS_DEBUG "/D_DEBUG /MDd /Zi /Ob0 /Od /RTC1" CACHE STRING "extra compiler flags for debug" FORCE )
else(WIN32)
  set(CMAKE_C_FLAGS_DEBUG "-pedantic " CACHE STRING "extra compiler flags for debug" FORCE )
  if(LINUX)
    set(CMAKE_C_FLAGS "-fPIC -g3 -Wall -pipe -DVTK_EXCLUDE_STRSTREAM_HEADERS -Wl,--no-undefined" CACHE STRING "compiler flags always used" FORCE)
    set(CMAKE_CXX_FLAGS "-fPIC -Wall -pipe -DVTK_EXCLUDE_STRSTREAM_HEADERS -Wl,--no-undefined" CACHE STRING "compiler flags always used" FORCE)
  else(LINUX)
    set(CMAKE_C_FLAGS "-fPIC -g3 -Wall -pipe -DVTK_EXCLUDE_STRSTREAM_HEADERS" CACHE STRING "compiler flags always used" FORCE)
    set(CMAKE_CXX_FLAGS "-fPIC -Wall -pipe -DVTK_EXCLUDE_STRSTREAM_HEADERS" CACHE STRING "compiler flags always used" FORCE)
  endif(LINUX)
  set(CMAKE_CXX_FLAGS_DEBUG "-g3 " CACHE STRING "extra compiler flags for debug" FORCE )
endif(WIN32)


message(STATUS "CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH}")

set( KSlice_include  ${CMAKE_CURRENT_SOURCE_DIR}/util/vrcl/include
                     ${CMAKE_CURRENT_SOURCE_DIR}/KViewerMain/include
                     ${CMAKE_CURRENT_SOURCE_DIR}/KSegmentor/include
                     ${CMAKE_CURRENT_SOURCE_DIR}/sfm_local_chanvese/include
)


# Use the include path and library for Qt that is used by VTK.
include_directories( ${QT_include_DIR} ${KSlice_include} )

#add_subdirectory(util/vrcl)
add_subdirectory(KViewerMain)
add_subdirectory(KSegmentor)
add_subdirectory(sfm_local_chanvese)
#add_subdirectory(dijkstra_growcut)
add_subdirectory(fastGrowCut)

message(STATUS "Check that this dir actually contains libraries! On windows, will have subdirs! ${KSLICE_LIB_PATH}" )
if(KSlice_Build_SlicerWrap)
  add_subdirectory(carreraslice_wrap)
  add_subdirectory(KViewerMain/exes)    # STANDALONE
endif()

set(CMAKE_INSTALL_PREFIX  ${Slicer_DIR} CACHE PATH  "cmake install prefix")

message(STATUS " Compiler: ${CMAKE_COMPILER} ")
message(STATUS " C++ flags (Release): ${CMAKE_CXX_FLAGS} ${CMAKE_CXX_FLAGS_RELEASE}")
message(STATUS " C++ flags (Debug): ${CMAKE_CXX_FLAGS} ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS " QT_LIBRARIES: ${QT_LIBRARIES} " )
message(STATUS " qt include dir: ${QT_include_DIR} " )

#-----------------------------------------------------------------------------
if(KSlice_Build_SlicerWrap)
  include(${Slicer_EXTENSION_CPACK})
endif()
