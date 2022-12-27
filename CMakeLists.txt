cmake_minimum_required(VERSION 3.1.3)
project(moveit_custom_perception)

find_package(Boost REQUIRED thread)

find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  moveit_core
  roscpp
  rosconsole
  urdf
  message_filters
  tf2
  tf2_eigen
  tf2_geometry_msgs
  tf2_ros
  pluginlib
  image_transport
  object_recognition_msgs
  sensor_msgs
  moveit_msgs
  moveit_ros_occupancy_map_monitor
  moveit_ros_planning
  moveit_ros_perception
  nodelet
)

find_package(Eigen3 REQUIRED)
find_package(OpenMP REQUIRED)
find_package(OpenCV)

set(PACKAGE_LIBRARIES
  custom_octomap_updater_core
  custom_octomap_updater
)

catkin_package(
  INCLUDE_DIRS
    include
  LIBRARIES
    ${PACKAGE_LIBRARIES}
  CATKIN_DEPENDS
    image_transport
    moveit_core
    moveit_msgs
    moveit_ros_occupancy_map_monitor
    moveit_ros_perception
    moveit_ros_planning
    nodelet
    object_recognition_msgs
    roscpp
    sensor_msgs
    tf2_geometry_msgs
  DEPENDS
    EIGEN3
)

include_directories(include
                    ${Boost_INCLUDE_DIRS}
                    ${catkin_INCLUDE_DIRS}
                    )
include_directories(SYSTEM
                    ${OpenCV_INCLUDE_DIRS}
                    ${EIGEN3_INCLUDE_DIRS}
                    )

set(LIB_NAME custom_octomap_updater)

add_library(${LIB_NAME}_core src/custom_octomap_updater.cpp)
set_target_properties(${LIB_NAME}_core PROPERTIES VERSION "${${PROJECT_NAME}_VERSION}")
target_link_libraries(${LIB_NAME}_core ${catkin_LIBRARIES} ${Boost_LIBRARIES})
set_target_properties(${LIB_NAME}_core PROPERTIES COMPILE_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
set_target_properties(${LIB_NAME}_core PROPERTIES LINK_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")

add_library(${LIB_NAME} src/plugin_init.cpp)
set_target_properties(${LIB_NAME} PROPERTIES VERSION "${${PROJECT_NAME}_VERSION}")
target_link_libraries(${LIB_NAME} ${LIB_NAME}_core ${catkin_LIBRARIES} ${Boost_LIBRARIES})

install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

install(TARGETS ${LIB_NAME} ${LIB_NAME}_core
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

install(
  FILES
    custom_octomap_updater_plugin_description.xml
  DESTINATION
    ${CATKIN_PACKAGE_SHARE_DESTINATION})
