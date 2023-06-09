cmake_minimum_required(VERSION 3.0.0)
project(xsens_mvn_ros)

set(CMAKE_BUILD_TYPE Release)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  geometry_msgs
  sensor_msgs
  std_msgs
  tf2_ros
  urdf
  eigen_conversions
  roslib
  xsens_mvn_ros_msgs
)

catkin_package(
  CATKIN_DEPENDS
    roscpp
    geometry_msgs
    sensor_msgs
    std_msgs
    tf2_ros
    urdf
    eigen_conversions
    roslib
    xsens_mvn_ros_msgs

  INCLUDE_DIRS 
    include 
)

include_directories(
    include
    xsens_mvn_sdk/include
    ${catkin_INCLUDE_DIRS}
)

add_subdirectory(xsens_mvn_sdk)

## Declare a C++ library
add_library(${PROJECT_NAME}_HumanDataHandler SHARED src/xsens_client/HumanDataHandler.cpp)
add_dependencies(${PROJECT_NAME}_HumanDataHandler ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME}_HumanDataHandler ${catkin_LIBRARIES})

add_library(${PROJECT_NAME}_XSensClient SHARED src/xsens_client/XSensClient.cpp src/xsens_client/Socket.cpp)
add_dependencies(${PROJECT_NAME}_XSensClient ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME}_XSensClient ${catkin_LIBRARIES} ${PROJECT_NAME}_HumanDataHandler ${PROJECT_NAME}_SDK)

## Declare executables
add_executable(${PROJECT_NAME}_xsens_client_node src/xsens_client_node.cpp)
set_target_properties(${PROJECT_NAME}_xsens_client_node PROPERTIES OUTPUT_NAME "xsens_client")
add_dependencies(${PROJECT_NAME}_xsens_client_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME}_xsens_client_node ${PROJECT_NAME}_XSensClient)

# Install headers and libraries
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

# Install launch files
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE
)