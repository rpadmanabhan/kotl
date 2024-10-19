cmake_minimum_required(VERSION 3.15)

project(kotl VERSION 0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS_OFF)


include(ExternalProject)

ExternalProject_Add(ddcutil_project
    PREFIX ${CMAKE_BINARY_DIR}/ddcutil
    SOURCE_DIR ${CMAKE_SOURCE_DIR}/external/ddcutil
    CONFIGURE_COMMAND ${CMAKE_COMMAND} -E env bash -c "cd ${CMAKE_SOURCE_DIR}/external/ddcutil && ./autogen.sh && ./configure --disable-shared --prefix=${CMAKE_BINARY_DIR}/ddcutil/install"
    BUILD_COMMAND ${CMAKE_COMMAND} -E env bash -c "cd ${CMAKE_SOURCE_DIR}/external/ddcutil && make -j 4"
    INSTALL_COMMAND ${CMAKE_COMMAND} -E env bash -c "cd ${CMAKE_SOURCE_DIR}/external/ddcutil && make install"
    BUILD_BYPRODUCTS ${CMAKE_BINARY_DIR}/ddcutil/install/lib/libddcutil.a
)



# Main executable
add_executable(kotl src/main.cpp)

# Link ddcutil library built by ExternalProject_Add
add_dependencies(kotl ddcutil_project)

# Include the headers from the installed ddcutil project
target_include_directories(kotl PRIVATE ${CMAKE_BINARY_DIR}/ddcutil/install/include)

# Link the static ddcutil library from the install directory
target_link_libraries(kotl PRIVATE ${CMAKE_BINARY_DIR}/ddcutil/install/lib/libddcutil.a)
