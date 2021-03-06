cmake_minimum_required(VERSION 2.8.3)
project(realm_stages)

# Fix to avoid OpenCV package confusion with ROS melodic
find_package(OpenCV 3.2 EXACT)
if (NOT OpenCV_FOUND)
    find_package(OpenCV 3)
endif()
find_package(cmake_modules REQUIRED)
find_package(catkin REQUIRED COMPONENTS
        realm_core
        realm_io
        realm_vslam_base
        realm_densifier_base
        realm_ortho
        )

####################
## Catkin Package ##
####################

catkin_package(
        INCLUDE_DIRS
            include
        LIBRARIES
            ${PROJECT_NAME}
        CATKIN_DEPENDS
            realm_core
            realm_io
            realm_vslam_base
            realm_densifier_base
            realm_ortho
        DEPENDS
            OpenCV
            cmake_modules
)

###################
## Build Library ##
###################

include_directories(
        include
        ${catkin_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
        ${cmake_modules_INCLUDE_DIRS}
)
add_library(${PROJECT_NAME} SHARED
        src/realm_stages_lib/stage_base.cpp
        src/realm_stages_lib/conversions.cpp
        src/realm_stages_lib/stage_settings_factory.cpp
        src/realm_stages_lib/pose_estimation.cpp
        src/realm_stages_lib/densification.cpp
        src/realm_stages_lib/surface_generation.cpp
        src/realm_stages_lib/ortho_rectification.cpp
        src/realm_stages_lib/mosaicing.cpp
        )
target_link_libraries(${PROJECT_NAME}
        ${catkin_LIBRARIES}
        ${cmake_modules_LIBRARIES}
        ${OpenCV_LIBRARIES}
)
add_definitions(
        -std=c++11
)

#####################
## Install Library ##
#####################

# Mark executables and/or libraries for installation
install(
        TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# Mark cpp header files for installation
install(
        DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
        FILES_MATCHING PATTERN "*.h"
)