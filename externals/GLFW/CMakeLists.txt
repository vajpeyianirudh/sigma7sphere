################################################################################
#
#  Copyright (C) 2001-2024 Force Dimension, Switzerland.
#  All Rights Reserved.
#
#  $PROJECT $VERSION
#  $DESCRIPTION
#
#  THIS FILE CAN NOT BE COPIED AND/OR DISTRIBUTED WITHOUT EXPRESS
#  PERMISSION FROM FORCE DIMENSION.
#
################################################################################

################################################################################
#
# Project configuration
#
################################################################################

cmake_minimum_required (VERSION 3.0.0)
project (GLFW)

# Version info
file (READ ${PROJECT_SOURCE_DIR}/project VERSION_FILE)
STRING (REGEX REPLACE ".*MAJOR=([0-9]+).*" "\\1" MAJOR "${VERSION_FILE}")
STRING (REGEX REPLACE ".*MINOR=([0-9]+).*" "\\1" MINOR "${VERSION_FILE}")
STRING (REGEX REPLACE ".*PATCH=([0-9]+).*" "\\1" PATCH "${VERSION_FILE}")
STRING (REGEX REPLACE ".*PRERELEASE=([0-9]+).*" "\\1" PRERELEASE "${VERSION_FILE}")
set (PROJECT_VERSION ${MAJOR}.${MINOR}.${PATCH}${PRERELEASE})

# OpenGL dependency
find_package (OpenGL REQUIRED)
include_directories (OPENGL_INCLUDE_DIR)

# Enforce build type.
if (NOT CMAKE_BUILD_TYPE)
  set (CMAKE_BUILD_TYPE Release CACHE STRING "Setting build mode to Release" FORCE)
endif()

################################################################################
# 
# Build products.
# 
################################################################################

# Header search path
include_directories (${PROJECT_SOURCE_DIR}/include)

# Static library source files
set (src ${PROJECT_SOURCE_DIR}/src)
set (sources ${src}/context.c ${src}/init.c ${src}/input.c ${src}/monitor.c ${src}/vulkan.c ${src}/window.c ${src}/null_init.c ${src}/null_joystick.c ${src}/null_monitor.c ${src}/null_window.c ${src}/platform.c)
if (${CMAKE_SYSTEM_NAME} MATCHES Windows)
    add_definitions (-D_GLFW_WIN32 -D_GLFW_USE_HYBRID_HPG)
    set (sources ${sources} ${src}/win32_init.c ${src}/win32_joystick.c ${src}/win32_monitor.c ${src}/win32_time.c ${src}/win32_thread.c ${src}/win32_window.c ${src}/wgl_context.c ${src}/egl_context.c ${src}/osmesa_context.c ${src}/win32_module.c) 
elseif (${CMAKE_SYSTEM_NAME} MATCHES Linux)
    add_definitions (-D_GLFW_X11)
    set (sources ${sources} ${src}/x11_init.c ${src}/x11_monitor.c ${src}/x11_window.c ${src}/xkb_unicode.c ${src}/posix_module.c ${src}/posix_poll.c ${src}/posix_time.c ${src}/posix_thread.c ${src}/glx_context.c ${src}/egl_context.c ${src}/osmesa_context.c ${src}/linux_joystick.c)
elseif (${CMAKE_SYSTEM_NAME} MATCHES Darwin)
    add_definitions (-D_GLFW_COCOA)
    set (sources ${sources} ${src}/cocoa_init.m ${src}/cocoa_joystick.m ${src}/cocoa_monitor.m ${src}/cocoa_window.m ${src}/cocoa_time.c ${src}/posix_module.c ${src}/posix_thread.c ${src}/nsgl_context.m ${src}/egl_context.c ${src}/osmesa_context.c)
endif ()

# Static library
add_library (glfw STATIC ${sources})

# Library dependencies and paths
set (GLFW_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/include")
set (GLFW_LIBRARIES glfw ${OPENGL_LIBRARIES})
if (${CMAKE_SYSTEM_NAME} MATCHES Windows)
    set (GLFW_LIBRARIES ${GLFW_LIBRARIES} "")
elseif (${CMAKE_SYSTEM_NAME} MATCHES Darwin)
    set (GLFW_LIBRARIES ${GLFW_LIBRARIES} "-framework Cocoa -framework CoreVideo")
elseif (${CMAKE_SYSTEM_NAME} MATCHES Linux) 
    set (GLFW_LIBRARIES ${GLFW_LIBRARIES} "X11;Xcursor;Xrandr;Xinerama")
endif ()

# Expose to enclosing project.
set (GLFW_INCLUDE_DIRS ${GLFW_INCLUDE_DIRS} PARENT_SCOPE)
set (GLFW_LIBRARIES ${GLFW_LIBRARIES} PARENT_SCOPE)

################################################################################
# 
# Export package
# 
################################################################################

# Export package for use from the build tree.
export (TARGETS glfw FILE ${PROJECT_BINARY_DIR}/GLFWTargets.cmake)
export (PACKAGE GLFW)

# Package definitions
set (CONF_INCLUDE_DIRS ${GLFW_INCLUDE_DIRS})
set (CONF_LIBRARIES ${GLFW_LIBRARIES})

# Package configuration and version files
configure_file (GLFWConfig.cmake.in "${PROJECT_BINARY_DIR}/GLFWConfig.cmake" @ONLY)
configure_file (GLFWConfigVersion.cmake.in "${PROJECT_BINARY_DIR}/GLFWConfigVersion.cmake" @ONLY)
