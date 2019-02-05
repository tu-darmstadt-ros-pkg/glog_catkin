cmake_minimum_required(VERSION 2.8.3)
project(glog_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

set(VERSION 0.3.5)
ExternalProject_Add(glog_src
  URL https://github.com/google/glog/archive/v${VERSION}.zip
  UPDATE_COMMAND ""
  PATCH_COMMAND patch -p0 < ${CMAKE_SOURCE_DIR}/fix-unused-typedef-warning.patch
  CONFIGURE_COMMAND cd ../glog_src-build/ &&
     cmake ../glog_src/.
       -DCMAKE_INSTALL_PREFIX:PATH=${CATKIN_DEVEL_PREFIX}
       -DBUILD_SHARED_LIBS:BOOL=true
       -DGLOG_GFLAGS_NAMESPACE:STRING=google
       -DCMAKE_BUILD_TYPE:STRING=Release
  BUILD_COMMAND cd ../glog_src-build/ && make -j 8
  INSTALL_COMMAND cd ../glog_src-build/ && make install -j 8
)

install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/glog
        DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h")
install(DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib/
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        FILES_MATCHING PATTERN "libglog*")
cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
          LIBRARIES glog)
