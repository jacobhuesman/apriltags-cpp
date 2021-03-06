# swatbotics/apriltags-cpp/CMakeLists.txt

cmake_minimum_required(VERSION 2.6)

project(APRILTAGS)

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR})

if(APPLE)
  include_directories(/opt/local/include) # MacPorts
  link_directories(/opt/local/lib)
  find_library(OPENGL_LIBRARY OpenGL)
else()
  find_library(OPENGL_LIBRARY GL)
  find_library(GLU_LIBRARY GLU)
  set(OPENGL_LIBRARY ${OPENGL_LIBRARY} ${GLU_LIBRARY})
endif()

find_library(GLUT_LIBRARY glut)

include(FindPkgConfig)
include(GNUInstallDirs)

find_package(OpenCV 3 REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})
MESSAGE( STATUS "OPENCV_INCLUDE_DIRS:         ${OpenCV_INCLUDE_DIRS}" )

pkg_check_modules(CAIRO cairo)

if (${CAIRO_FOUND})
add_definitions(-DMZ_HAVE_CAIRO)
endif ()

find_package( CGAL QUIET COMPONENTS  )

if (CGAL_FOUND)
  message("-- CGAL found")
  #set(TMP_CXX_FLAGS ${CXX_FLAGS})
  include( ${CGAL_USE_FILE} )
  #set(CXX_FLAGS ${TMP_CXX_FLAGS} " " ${CXX_FLAGS})
  add_definitions(-DHAVE_CGAL)
  find_package( Boost REQUIRED )
else()
  message("CGAL not found, can not use new quad detector")
endif()

set(CMAKE_C_FLAGS "-Wall -g")
set(CMAKE_CXX_FLAGS "-Wall -g")

if (APPLE OR UNIX)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wsign-compare -frounding-math")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wsign-compare -frounding-math")
endif()

set(CMAKE_C_FLAGS_DEBUG "-O")
set(CMAKE_CXX_FLAGS_DEBUG "-O")

set(CMAKE_C_FLAGS_RELEASE "-O2")
set(CMAKE_CXX_FLAGS_RELEASE "-O2")

add_subdirectory(src)

