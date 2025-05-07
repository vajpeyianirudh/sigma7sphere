cmake_minimum_required(VERSION 3.16)
project(SigmaTest)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/src/sphere.cpp)

find_package(OpenGL REQUIRED)
#find_package(glfw3)

target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_link_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/lib)

target_link_libraries(${PROJECT_NAME} PRIVATE dhd drd GL glfw GLU usb-1.0 pthread)

target_link_libraries(${PROJECT_NAME} PRIVATE OpenGL::OpenGL) 