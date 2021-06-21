cmake_minimum_required(VERSION 2.8.3)
set(PROJECT_NAME gtest_ros_example)
project(${PROJECT_NAME})
#option(build_gtest_samples "Build gtest's sample programs." OFF)
#message("BUILD_TESTS=${BUILD_TESTS}")
SET(BUILD_TESTS False CACHE BOOL "Some user-specified option")
# kinetic specific
add_compile_options(-std=c++11)

# Find packages and includes
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
)
find_package(GTest REQUIRED)

include_directories(
  include/${PROJECT_NAME}
  ${catkin_INCLUDE_DIRS}
  ${GTEST_INCLUDE_DIRS}
)

catkin_package()

# Add common talker and listener executables
add_executable(rostalker src/rostalker.cpp)
add_executable(roslistener src/roslistener.cpp)
target_link_libraries(roslistener
  ${catkin_LIBRARIES} pthread
)
target_link_libraries(rostalker
  ${catkin_LIBRARIES} pthread
)

#############
## Testing ##
#############

file(GLOB RESULT ${CMAKE_CURRENT_SOURCE_DIR}/../test/)
list(LENGTH RESULT RES_LEN)
if(BUILD_TESTS)
	message("BUILDING THE TESTS")
	file(GLOB gtests_SRC RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/../test/${PROJECT_NAME}/" "${CMAKE_CURRENT_SOURCE_DIR}/../test/${PROJECT_NAME}/*.cpp" )
	foreach (testsourcefile ${gtests_SRC})
		message(${testsourcefile})
# Build and link the test_talker
#if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/../test)
		string( REPLACE ".cpp" "" testname ${testsourcefile} )	
		catkin_add_gtest(${testname} ../test/${PROJECT_NAME}/${testsourcefile})
		target_link_libraries(${testname} ${catkin_LIBRARIES})
	endforeach()
#####################################
## Testing w/ and w/o catkin magic ##
#####################################

# Build and link the simple_test executable w/ catkin magic
	#catkin_add_gtest(simple_test_catkin ../test/${PROJECT_NAME}/simple_test.cpp)
	#target_link_libraries(simple_test_catkin ${catkin_LIBRARIES})
endif()
# Build and link the simple_test executable w/o any catkin magic
add_executable(simple_test2 src/simple_test.cpp)
target_link_libraries(simple_test2 ${GTEST_LIBRARIES} pthread)
