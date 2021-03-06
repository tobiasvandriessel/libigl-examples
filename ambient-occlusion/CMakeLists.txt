cmake_minimum_required(VERSION 2.8.6)

SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/../cmake)

# We need C++11. Put this directive after CGAL's include.
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -g -Wall -Wextra -Wno-reorder -Wno-unknown-pragmas -msse4.2 -Wno-deprecated-declarations -D__ASSERT_MACROS_DEFINE_VERSIONS_WITHOUT_UNDERSCORES=0 -DNO_2D" )
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
endif()

find_package(EIGEN REQUIRED)
include_directories( "${EIGEN_INCLUDE_DIR}" )
# Must be found before trying to find libraries in libigl/external
find_package(LIBIGL REQUIRED)
include_directories( "${LIBIGL_INCLUDE_DIR}" )
find_package(EMBREE QUIET)

if(EMBREE_FOUND)
  include_directories( "${EMBREE_INCLUDE_DIR}" )
  add_subdirectory("${EMBREE_INCLUDE_DIR}/../" "embree")    
  list(APPEND LIBS "embree")
  add_definitions(-DIGL_WITH_EMBREE)
endif(EMBREE_FOUND)

find_package(ANTTWEAKBAR REQUIRED)
include_directories( ${ANT_TWEAK_BAR_INCLUDE_DIR} )
find_package(OpenGL REQUIRED)
find_package(GLUT REQUIRED)

link_directories(
  /usr/local/lib
  /opt/local/lib
  ${ANT_TWEAK_BAR_DIRS}
  ${EIGEN_DIRS}
)

list(APPEND LIBS 
  ${LIBIGL_LIBRARIES} 
  ${OPENGL_LIBRARIES} 
  ${GLUT_LIBRARIES}
  ${ANT_TWEAK_BAR_LIBRARY}
)

FILE(GLOB SRCFILES *.cpp)
add_executable(ambient_occlusion ${SRCFILES})
target_link_libraries(ambient_occlusion ${LIBS})
