cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(Tutorial VERSION 1.0)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# Use add_subdirectory() to add MathFunctions to this project
# <build-dir> takes <source-dir> relative path value before expanding (i.e. MathFunctions)
add_subdirectory(MathFunctions)

# add the executable
add_executable(Tutorial tutorial.cxx)

# Use target_link_libraries to link the library to our executable
target_link_libraries(Tutorial PUBLIC MathFunctions)

# Add MathFunctions to Tutorial's target_include_directories()
# Hint: ${PROJECT_SOURCE_DIR} is a path to the project source. AKA This folder!

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"                 # to find TutorialConfig.h
                           "${PROJECT_SOURCE_DIR}/MathFunctions")  # to find MathFunctions.h
