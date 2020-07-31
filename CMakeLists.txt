cmake_minimum_required(VERSION 2.8.3)
project(template)

###################################################################
##1.                       Compile Option                        ##
###################################################################
##CMAKE_CXX_FLAGS
#(1) -O3优化
#(2) -march=native:自动选择合适的cpu优化
#(3) sse加速

SET(CMAKE_BUILD_TYPE "Release")                         #
SET(CMAKE_CXX_FLAGS "-std=c++11 \
    -msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2    \
    -march=native -O3 -Wall")
add_compile_options(-std=c++11)                         # C++11支持


###################################################################
##2.              Find_Package && INCLUDE                        ##
###################################################################

# ROS的package
find_package(catkin REQUIRED COMPONENTS
  rospy
  roscpp
  std_msgs
  std_srvs
  message_generation
)

# 普通C++的库
find_package(Boost REQUIRED COMPONENTS system)

include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)

include_directories(cmake/eigen.cmake)


###################################################################
##3.                       Catkin，相关                           ##
###################################################################

## 如果包含了.py的启动脚本，那么取消注释
## 这样 运行rosrun/roslaunch  ...package   ...node 的时候可以找到
# catkin_python_setup()

#####################################################
##3.(1) Declare ROS messages, services and actions ##
#####################################################

## 如果项目用到了自定义的msg，srv，action
## 1. 在package.xml中添加
##      <build_depend>message_generation</build_depend>
##      <exec_depend>message_runtime</exec_depend>

## 2. 自定义msg
add_message_files(
    FILES
    my_msg.msg
)

## 3. 自定义srv
add_service_files(
    FILES
    my_srv.srv
)

## 4. Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## 5. 如果自定义的msg或srv包含了ros标准库的某些msg，需要在这里添加依赖
generate_messages(
    DEPENDENCIES
    std_msgs  # Or other packages containing msgs
)

#####################################################
##3.(2) Declare ROS dynamic reconfigure parameters ##
#####################################################

## To declare and build dynamic reconfigure parameters within this
## package, follow these steps:
## * In the file package.xml:
##   * add a build_depend and a exec_depend tag for "dynamic_reconfigure"
## * In this file (CMakeLists.txt):
##   * add "dynamic_reconfigure" to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * uncomment the "generate_dynamic_reconfigure_options" section below
##     and list every .cfg file to be processed

## Generate dynamic reconfigure parameters in the 'cfg' folder
# generate_dynamic_reconfigure_options(
#   cfg/DynReconf1.cfg
#   cfg/DynReconf2.cfg
# )

####################################################
##3.(3)        catkin specific configuration      ##
####################################################
## catkin_package 为这个package 生成对应的 .cmake文件 ====> 作为外部库引用
## INCLUDE_DIRS:    需要导出的头文件的路径
## LIBRARIES:       导出的库名
## CATKIN_DEPENDS:  本package编译时，依赖哪些ros标准库
## DEPENDS:         本package编译时，依赖哪些通用C++库

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES template
#  CATKIN_DEPENDS rospy roscpp
#  DEPENDS system_lib
)

###################################################################
##4.                       Target_Link                           ##
###################################################################

## 设置需要链接的所有库
set(ALL_TARGET_LINK_LIBRARIES "")
list(APPEND ALL_TARGET_LINK_LIBRARIES)


###################################################################
##5.                    Build && Export                          ##
###################################################################

##################################
## 将本package导出为库
# add_library(${PROJECT_NAME}
#   src/${PROJECT_NAME}/template.cpp
# )
## 添加本package导出库的依赖库，例如，如果本项目导出库需要先构建自定义的msg，srv等，则需要在这里取消注释即可
# add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
##################################


##################################
## 可执行文件
add_executable(${PROJECT_NAME}_node src/template_node.cpp)

## 添加可执行文件所需的依赖库，例如，如果可执行文件需要先构建自定义的msg，srv等，则需要在这里取消注释即可
# add_dependencies(${PROJECT_NAME}_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
## 链接库
target_link_libraries(${PROJECT_NAME}_node
    ${catkin_LIBRARIES}
)
##################################

############################################不常用################################################

#############
## Install ##
#############

## 标记Python运行脚本安装路径
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_executables.html
# install(TARGETS ${PROJECT_NAME}_node
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark libraries for installation
## See http://docs.ros.org/melodic/api/catkin/html/howto/format1/building_libraries.html
# install(TARGETS ${PROJECT_NAME}
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_template.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
