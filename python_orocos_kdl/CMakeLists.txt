# http://ros.org/doc/groovy/api/catkin/html/user_guide/supposed.html
cmake_minimum_required(VERSION 2.8.3)

set (ROS_ROOT $ENV{ROS_ROOT} )
if (ROS_ROOT)
  # Build with ROS (Catkin)
  # NOTE: different CMake project names might be required for OROCOS integration
  project(python_orocos_kdl)

  # Load catkin and all dependencies required for this package
  find_package(catkin REQUIRED COMPONENTS orocos_kdl)

  catkin_package(
    DEPENDS orocos_kdl
    #INCLUDE_DIRS include
    #LIBRARIES ${PROJECT_NAME}
    )

  message("include dirs: ${catkin_INCLUDE_DIRS}")

  include_directories(${catkin_INCLUDE_DIRS})
  link_directories(${catkin_LIBRARY_DIRS})

  catkin_python_setup()
else()
  # Build without ROS
  # NOTE: different CMake project names might be required for OROCOS integration
  project(python-orocos-kdl) 
  find_package(Orocos-KDL)
  include_directories(${Orocos-KDL_INCLUDE_DIRS})
  link_directories(${Orocos-KDL_LIBRARY_DIRS})
endif()

# Get python CMake vars
find_package(PythonInterp REQUIRED)
find_package(PythonLibs REQUIRED)

# Get SIP for building PyKDL library
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
find_package(SIP REQUIRED)
include(SIPMacros)
include_directories(${SIP_INCLUDE_DIR} ${PYTHON_INCLUDE_DIRS})

file(GLOB SIP_FILES "${CMAKE_CURRENT_SOURCE_DIR}/*.sip")
set(SIP_INCLUDES ${SIP_FILES})
set(SIP_EXTRA_OPTIONS "-o")
set(PYTHON_PACKAGES_DIR_NAME "dist-packages")
set(PYTHON_SITE_PACKAGES_INSTALL_DIR
  ${CMAKE_INSTALL_PREFIX}/lib${LIB_SUFFIX}/python${PYTHON_VERSION_MAJOR}.${PYTHON_VERSION_MINOR}/${PYTHON_PACKAGES_DIR_NAME}
  CACHE PATH "The directory where python modules will be installed to.")

add_sip_python_module(PyKDL PyKDL/PyKDL.sip orocos-kdl)
