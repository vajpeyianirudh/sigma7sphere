cmake_minimum_required(VERSION 3.16)
project(SigmaTest)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_EXTENSIONS OFF)

# Add external GLFW project.
add_subdirectory (${PROJECT_SOURCE_DIR}/externals/GLFW)

# Import external packages.
find_package(GLFW REQUIRED)

# Add the sphere application.
add_executable(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/src/sphere.cpp)
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include ${GLFW_INCLUDE_DIRS} externals/Eigen)
target_link_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/lib)
if(WIN32)
    target_link_libraries(${PROJECT_NAME} PRIVATE drd64 ${GLFW_LIBRARIES})
elseif(UNIX)
    target_link_libraries(${PROJECT_NAME} PRIVATE drd ${GLFW_LIBRARIES} usb-1.0)
endif()
