cmake_minimum_required(VERSION 3.20)

# M4 CRITICAL INJECTION - Page Size Optimization
add_definitions(-DPAGE_SIZE_16KB -DMETAL -DJIT)

project(Silicon-PS2-Stable VERSION 1.0.0 LANGUAGES CXX Swift)

# Set CMake module path for Thiago's build system
set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

# iOS Configuration
set(CMAKE_SYSTEM_NAME iOS)
set(CMAKE_OSX_ARCHITECTURES arm64)
set(CMAKE_OSX_DEPLOYMENT_TARGET 15.0)
set(CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM "")
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer")
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGNING_REQUIRED NO)
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGNING_ALLOWED NO)

# Build settings
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DPAGE_SIZE_16KB -fno-strict-aliasing")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DPAGE_SIZE_16KB -std=c++17 -fno-strict-aliasing")

# Enable Swift
enable_language(Swift)

# Include CMake utilities from Thiago's structure
include(${CMAKE_MODULE_PATH}/BuildParameters.cmake)
include(${CMAKE_MODULE_PATH}/Pcsx2Utils.cmake)

# Add cpp subdirectory for native build
add_subdirectory(cpp)
