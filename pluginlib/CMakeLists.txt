cmake_minimum_required(VERSION 2.8.3)
project(pluginlib)

hunter_add_package(catkin)
hunter_add_package(class_loader)
hunter_add_package(ros)
hunter_add_package(rosconsole)
hunter_add_package(ros_console_bridge)
hunter_add_package(Boost COMPONENTS filesystem system)
hunter_add_package(tinyxml2)

find_package(catkin REQUIRED COMPONENTS class_loader rosconsole roslib)
find_package(Boost CONFIG REQUIRED COMPONENTS filesystem system)
find_package(tinyxml2 CONFIG REQUIRED)

set(PROJECT_INSTALLSPACE_LIBRARIES ros::${PROJECT_NAME})
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS class_loader rosconsole roslib
  DEPENDS "Boost COMPONENTS filesystem system" tinyxml2 console_bridge
)

add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)
target_link_libraries(${PROJECT_NAME} INTERFACE ${catkin_LIBRARIES})

if(CATKIN_ENABLE_TESTING)
  include_directories(include ${catkin_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS} ${TinyXML2_INCLUDE_DIRS})

  add_library(test_plugins EXCLUDE_FROM_ALL SHARED test/test_plugins.cpp)
  target_link_libraries(test_plugins ${PROJECT_NAME})

  catkin_add_gtest(${PROJECT_NAME}_utest test/utest.cpp)
  if(TARGET ${PROJECT_NAME}_utest)
    target_link_libraries(${PROJECT_NAME}_utest ${TinyXML2_LIBRARIES} ${catkin_LIBRARIES} ${Boost_LIBRARIES})
    add_dependencies(${PROJECT_NAME}_utest test_plugins)
  endif()

  include(CheckCXXCompilerFlag)
  check_cxx_compiler_flag("-std=c++11" COMPILER_SUPPORTS_CXX11)
  if(COMPILER_SUPPORTS_CXX11)
    catkin_add_gtest(${PROJECT_NAME}_unique_ptr_test test/unique_ptr_test.cpp)
    if(TARGET ${PROJECT_NAME}_unique_ptr_test)
      target_link_libraries(${PROJECT_NAME}_unique_ptr_test ${TinyXML2_LIBRARIES} ${catkin_LIBRARIES} ${Boost_LIBRARIES})
      set_target_properties(${PROJECT_NAME}_unique_ptr_test PROPERTIES COMPILE_FLAGS -std=c++11 LINK_FLAGS -std=c++11)
      add_dependencies(${PROJECT_NAME}_unique_ptr_test test_plugins)
    endif()
  endif()

endif()

install(DIRECTORY include/pluginlib/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(PROGRAMS scripts/pluginlib_headers_migration.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(TARGETS ${PROJECT_NAME}
  EXPORT ${PROJECT_NAME}Targets
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(EXPORT ${PROJECT_NAME}Targets
    DESTINATION share/${PROJECT_NAME}/cmake
    NAMESPACE ros::)
