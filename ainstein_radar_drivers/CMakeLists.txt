cmake_minimum_required(VERSION 3.0.0)
set(CMAKE_CXX_STANDARD 14)

project(ainstein_radar_drivers)

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
find_package(catkin REQUIRED COMPONENTS
  roscpp
  nodelet
  std_msgs
  can_msgs
  socketcan_bridge
  tf2
  tf2_ros
  ainstein_radar_msgs
  ainstein_radar_filters
  dynamic_reconfigure
  message_generation
  )

add_message_files(
  FILES
  O79NetworkParams.msg
  O79DeviceParams.msg
  O79TrackingParams.msg
  )

generate_messages(
  DEPENDENCIES
  std_msgs
)

# Must be called after catkin_python_setup() and before catkin_package():
generate_dynamic_reconfigure_options(
  cfg/ZoneOfInterestT79.cfg
  cfg/O79Params.cfg
  )

catkin_package()

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIRS}
  )

add_executable(o79_can_node src/o79_can_node.cpp src/radar_interface_o79_can.cpp)
add_dependencies(o79_can_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(o79_can_node ${catkin_LIBRARIES})

add_executable(o79_udp_node src/o79_udp_node.cpp src/radar_interface_o79_udp.cpp src/radar_driver_o79_udp.cpp)
add_dependencies(o79_udp_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(o79_udp_node ${catkin_LIBRARIES} ${PCL_LIBRARIES})

add_executable(k79_node src/k79_node.cpp src/radar_interface_k79.cpp src/radar_driver_k79.cpp)
add_dependencies(k79_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(k79_node ${catkin_LIBRARIES})

add_library(k79_nodelet src/k79_nodelet.cpp src/radar_interface_k79.cpp src/radar_driver_k79.cpp)
add_dependencies(k79_nodelet ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(k79_nodelet ${catkin_LIBRARIES})

add_executable(k79_3d_node src/k79_3d_node.cpp src/radar_interface_k79_3d.cpp)
add_dependencies(k79_3d_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(k79_3d_node ${catkin_LIBRARIES})

add_library(k79_3d_nodelet src/k79_3d_nodelet.cpp src/radar_interface_k79_3d.cpp)
add_dependencies(k79_3d_nodelet ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(k79_3d_nodelet ${catkin_LIBRARIES})

add_executable(t79_node src/t79_node.cpp src/radar_interface_t79.cpp)
add_dependencies(t79_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} ${PROJECT_NAME}_gencfg)
target_link_libraries(t79_node ${catkin_LIBRARIES})

add_executable(t79_bsd_node src/t79_bsd_node.cpp src/radar_interface_t79_bsd.cpp)
add_dependencies(t79_bsd_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
target_link_libraries(t79_bsd_node ${catkin_LIBRARIES})

install(TARGETS
  o79_can_node
  o79_udp_node
  k79_node
  k79_nodelet
  k79_3d_node
  k79_3d_nodelet
  t79_node
  t79_bsd_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )

## Install project namespaced headers
install(DIRECTORY include/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE)
