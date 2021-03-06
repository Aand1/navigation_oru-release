cmake_minimum_required(VERSION 2.8.3)
project(orunav_path_pool)

find_package(catkin REQUIRED COMPONENTS
  cmake_modules
  roscpp
  std_msgs
  nav_msgs
  orunav_msgs
  orunav_generic
  orunav_rviz
  orunav_conversions
  orunav_geometry
)

find_package(Eigen3)
if(NOT EIGEN3_FOUND)
  # Fallback to cmake_modules
  find_package(cmake_modules REQUIRED)
  find_package(Eigen REQUIRED)
  set(EIGEN3_INCLUDE_DIRS ${EIGEN_INCLUDE_DIRS})
else()
  set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif()

find_package(TinyXML REQUIRED)

find_package(Boost REQUIRED COMPONENTS system program_options)

SET(CMAKE_CXX_FLAGS "-std=c++11 -O3")

catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES orunav_path_pool
#  CATKIN_DEPENDS TinyXML
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  ${TinyXML_INCLUDE_DIRS}
)

## Declare a C++ library
add_library(graph_astar SHARED
   src/graph_astar.cpp
)

add_executable(wef2path src/wef2path_main.cpp)
add_dependencies(wef2path ${catkin_EXPORTED_TARGETS})
target_link_libraries(wef2path
   ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${TinyXML_LIBRARIES} graph_astar
)

add_executable(path_pool_get_path_service src/get_path_service.cpp) 
add_dependencies(path_pool_get_path_service ${catkin_EXPORTED_TARGETS})
target_link_libraries(path_pool_get_path_service
   ${catkin_LIBRARIES} ${Boost_LIBRARIES} ${TinyXML_LIBRARIES} graph_astar
)
