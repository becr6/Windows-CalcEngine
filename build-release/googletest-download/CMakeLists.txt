cmake_minimum_required(VERSION 3.5)

project( googletest-download NONE )
 
# find_package(Threads REQUIRED)
include(ExternalProject)

ExternalProject_Add(GTestExternal
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG "v1.14.0"
    
    UPDATE_COMMAND ""
    PATCH_COMMAND ""
  	
    SOURCE_DIR "/Users/tgv/solar-shading/libs/Windows-CalcEngine/build-release/googletest-src"
    BINARY_DIR "/Users/tgv/solar-shading/libs/Windows-CalcEngine/build-release/googletest-build"
  	
    CONFIGURE_COMMAND ""
    BUILD_COMMAND ""
    TEST_COMMAND ""
    INSTALL_COMMAND ""
)
