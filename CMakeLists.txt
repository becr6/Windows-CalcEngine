# Modified CMakeLists.txt that:
#
# 1.	Sets install prefix to /usr/local
# 2.	Redirects headers to /usr/local/include/wce
# 3.	Ensures that fix-wce-includes.sh runs after installation. 
#		This depends on: $ENV{MPC_ROOT}/bin/fix-wce-includes.sh
# 4.	Skips all testing
# 5.	Ensures header include paths are relative to wce/

cmake_minimum_required(VERSION 3.20)
project(Windows_CalcEngine)

# Force install to /usr/local if not explicitly set
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX "/usr/local" CACHE PATH "Default install path" FORCE)
endif()

# Set C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Don't override build type - let parent project control it
if(NOT CMAKE_BUILD_TYPE AND NOT DEFINED CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

if(NOT EXISTS "$ENV{MPC_ROOT}/bin/fix-wce-includes.sh")
    message(FATAL_ERROR "Required script not found: $ENV{MPC_ROOT}/bin/fix-wce-includes.sh")
endif()


# Enable all submodules
set(BUILD_WCE_COMMON ON)
set(BUILD_WCE_GASES ON)
set(BUILD_WCE_VIEWER ON)
set(BUILD_WCE_THERMAL ON)
set(BUILD_WCE_OPTICAL ON)

# Collect sources
file(GLOB_RECURSE WCE_COMMON_SRC src/Common/src/*.cpp)
file(GLOB_RECURSE WCE_GASES_SRC src/Gases/src/*.cpp)
file(GLOB_RECURSE WCE_VIEWER_SRC src/Viewer/src/*.cpp)
file(GLOB_RECURSE WCE_THERMAL_SRC src/Tarcog/src/*.cpp)
file(GLOB_RECURSE WCE_OPTICAL_SRC
    src/SpectralAveraging/src/*.cpp
    src/SingleLayerOptics/src/*.cpp
    src/MultiLayerOptics/src/*.cpp
)

# Create the library
add_library(Windows-CalcEngine STATIC
    ${WCE_COMMON_SRC}
    ${WCE_GASES_SRC}
    ${WCE_VIEWER_SRC}
    ${WCE_THERMAL_SRC}
    ${WCE_OPTICAL_SRC}
)

target_include_directories(Windows-CalcEngine PUBLIC
    src/Common/include
    src/Gases/include
    src/Viewer/include
    src/Tarcog/include
    src/SpectralAveraging/include
    src/SingleLayerOptics/include
    src/MultiLayerOptics/include
)
# check existance of fix-wce-headers.sh
if(NOT EXISTS "$ENV{MPC_ROOT}/bin/fix-wce-includes.sh")
    message(FATAL_ERROR "Required script not found: $ENV{MPC_ROOT}/bin/fix-wce-includes.sh")
endif()

# Install the library
install(TARGETS Windows-CalcEngine
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

# Install headers under /usr/local/include/wce/
install(DIRECTORY src/Common/include/ DESTINATION include/wce)
install(DIRECTORY src/Gases/include/ DESTINATION include/wce)
install(DIRECTORY src/Viewer/include/ DESTINATION include/wce)
install(DIRECTORY src/Tarcog/include/ DESTINATION include/wce)
install(DIRECTORY src/SpectralAveraging/include/ DESTINATION include/wce)
install(DIRECTORY src/SingleLayerOptics/include/ DESTINATION include/wce)
install(DIRECTORY src/MultiLayerOptics/include/ DESTINATION include/wce)

# Run fix-wce-includes.sh after install
# install(CODE "
#   execute_process(
#     COMMAND $ENV{MPC_ROOT}/bin/fix-wce-includes.sh ${CMAKE_INSTALL_PREFIX}/include/wce
#     WORKING_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include/wce
#     RESULT_VARIABLE script_result
#   )
#   if(NOT script_result EQUAL 0)
#     message(FATAL_ERROR \"fix-wce-includes.sh failed with exit code \${script_result}\")
#   endif()
# ")
