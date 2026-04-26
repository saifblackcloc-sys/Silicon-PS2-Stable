cmake_minimum_required(VERSION 3.20)
project(Silicon-PS2-Stable VERSION 1.0.0 LANGUAGES CXX Swift)

# iOS Configuration
set(CMAKE_SYSTEM_NAME iOS)
set(CMAKE_OSX_ARCHITECTURES arm64)
set(CMAKE_OSX_DEPLOYMENT_TARGET 15.0)
set(CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM "")
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "iPhone Developer")
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGNING_REQUIRED NO)
set(CMAKE_XCODE_ATTRIBUTE_CODE_SIGNING_ALLOWED NO)

# Enable Swift
enable_language(Swift)

# Enable METAL and JIT flags
add_definitions(-DMETAL -DJIT)

# Source Files
file(GLOB_RECURSE CPP_SOURCES
    cpp/*.cpp
    cpp/*.h
    cpp/*.mm
)

file(GLOB_RECURSE SWIFT_SOURCES
    swift/*.swift
)

# Resources
set(RESOURCE_FILES
    assets/
)

# Executable
add_executable(Silicon-PS2-Stable
    ${CPP_SOURCES}
    ${SWIFT_SOURCES}
    ${RESOURCE_FILES}
)

# Include Directories
target_include_directories(Silicon-PS2-Stable PRIVATE
    cpp
    cpp/common/include
    cpp/pcsx2
)

# Compiler Flags
target_compile_options(Silicon-PS2-Stable PRIVATE
    -std=c++17
    -O3
    -DNDEBUG
)

# Link Libraries
target_link_libraries(Silicon-PS2-Stable
    "-framework Foundation"
    "-framework UIKit"
    "-framework Metal"
    "-framework AudioToolbox"
    "-framework AVFoundation"
    "-framework CoreGraphics"
    "-framework QuartzCore"
    "-framework OpenGLES"
)

# Bundle Resources
set_target_properties(Silicon-PS2-Stable PROPERTIES
    MACOSX_BUNDLE YES
    MACOSX_BUNDLE_GUI_IDENTIFIER "com.otti83.iPSX2"
    MACOSX_BUNDLE_BUNDLE_NAME "Silicon-PS2-Stable"
    MACOSX_BUNDLE_BUNDLE_VERSION "${PROJECT_VERSION}"
    MACOSX_BUNDLE_SHORT_VERSION_STRING "${PROJECT_VERSION}"
    RESOURCE "${RESOURCE_FILES}"
)

# Swift Settings
set_target_properties(Silicon-PS2-Stable PROPERTIES
    XCODE_ATTRIBUTE_SWIFT_VERSION "5.0"
    XCODE_ATTRIBUTE_SWIFT_OBJC_BRIDGING_HEADER "cpp/ios_main.h"
)