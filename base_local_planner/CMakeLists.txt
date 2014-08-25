cmake_minimum_required(VERSION 2.8.3)
project(base_local_planner)

find_package(catkin REQUIRED
        COMPONENTS
            cmake_modules
            roscpp
            tf
            message_generation
            dynamic_reconfigure
            nav_core
            rostest
            costmap_2d
            pluginlib
            angles
        )

find_package(Boost REQUIRED
    COMPONENTS
        thread
        )

find_package(Eigen REQUIRED)
include_directories(
    include
    ${catkin_INCLUDE_DIRS}
    ${EIGEN_INCLUDE_DIRS}
)
add_definitions(${EIGEN_DEFINITIONS})

catkin_python_setup()

# messages
add_message_files(
    DIRECTORY msg
    FILES
    Position2DInt.msg
)

generate_messages(
    DEPENDENCIES
        std_msgs
)

# dynamic reconfigure
generate_dynamic_reconfigure_options(
    cfg/BaseLocalPlanner.cfg
)

catkin_package(
    INCLUDE_DIRS include
    LIBRARIES
        base_local_planner
    CATKIN_DEPENDS
        roscpp
        dynamic_reconfigure
        message_generation
        tf
        pluginlib
        costmap_2d
        nav_core
        angles
)

#uncomment for profiling
#set(ROS_COMPILE_FLAGS "-g -pg" ${ROS_COMPILE_FLAGS})
#set(ROS_LINK_FLAGS "-g -pg" ${ROS_LINK_FLAGS})
#set(ROS_COMPILE_FLAGS "-g" ${ROS_COMPILE_FLAGS})
#set(ROS_LINK_FLAGS "-g" ${ROS_LINK_FLAGS})

add_library(base_local_planner
	src/footprint_helper.cpp
	src/goal_functions.cpp
	src/map_cell.cpp
	src/map_grid.cpp
	src/map_grid_cost_function.cpp
	src/latched_stop_rotate_controller.cpp
	src/local_planner_util.cpp
	src/odometry_helper_ros.cpp
	src/oscillation_cost_function.cpp
	src/prefer_forward_cost_function.cpp
	src/simple_scored_sampling_planner.cpp
	src/simple_trajectory_generator.cpp
	src/trajectory.cpp)
add_dependencies(base_local_planner base_local_planner_gencfg)
add_dependencies(base_local_planner base_local_planner_gencpp)
add_dependencies(base_local_planner nav_msgs_gencpp)
target_link_libraries(base_local_planner
    ${catkin_LIBRARIES}
    ${Boost_LIBRARIES}
    ${Eigen_LIBRARIES}
    )

install(TARGETS
            base_local_planner
       LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
       )

install(FILES blp_plugin.xml
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)
