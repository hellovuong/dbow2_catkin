cmake_minimum_required(VERSION 2.8.3)
project(dbow2_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(dbow2_src
  GIT_REPOSITORY https://github.com/hellovuong/DBoW2.git
  GIT_TAG master
  UPDATE_COMMAND ""
  #PATCH_COMMAND ""
  CONFIGURE_COMMAND  cd ../dbow2_src &&
                     cmake -DCMAKE_BUILD_TYPE=Release .

  BUILD_COMMAND cd ../dbow2_src && make
  INSTALL_COMMAND 
      mkdir -p ${CATKIN_DEVEL_PREFIX}/include/DBoW2 &&
      cp -r ../dbow2_src/DBoW2 ${CATKIN_DEVEL_PREFIX}/include/DBoW2 &&
      cp -r ../dbow2_src/DUtils ${CATKIN_DEVEL_PREFIX}/include/DBoW2 &&
      cp -r ../dbow2_src/lib ${CATKIN_DEVEL_PREFIX}/lib/DBoW2
)

cs_add_library(${PROJECT_NAME} SHARED src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} dbow2_src)
target_link_libraries(${PROJECT_NAME} 
  ${CATKIN_DEVEL_PREFIX}/lib/DBoW2/libDBoW2${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()
cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS dbow2-extras.cmake)
