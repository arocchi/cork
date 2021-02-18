cmake_minimum_required(VERSION 3.10)
project(cork VERSION 1.0)
SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMake)
include(GNUInstallDirs)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
add_library(cork SHARED
  src/file_formats/files.cpp
  src/file_formats/ifs.cpp
  src/file_formats/off.cpp
  src/isct/empty3d.cpp
  src/isct/quantization.cpp
  src/isct/triangle.c
  src/util/log.cpp
  src/util/timer.cpp
  src/cork.cpp)
add_executable(cork-bin src/main.cpp)
set(INCLUDEDIRS
  src/mesh
  src/accel/
  src/file_formats/
  src/isct/
  src/math/
  src/mesh/
  src/rawmesh/
  src/util/
)


set_property(TARGET cork PROPERTY POSITION_INDEPENDENT_CODE ON)
target_include_directories(cork PRIVATE ${INCLUDEDIRS})
target_include_directories(cork
  PUBLIC $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
target_include_directories(cork-bin PRIVATE ${INCLUDEDIRS})
IF (NOT GMP_FOUND)
    find_package(GMP REQUIRED)
ENDIF (NOT GMP_FOUND)
include_directories(${GMP_INCLUDE_DIRS})
target_link_libraries(cork ${GMP_LIBRARIES})
target_link_libraries(cork-bin PRIVATE cork)
target_compile_definitions(cork PRIVATE 
  -DNO_TIMER -DREDUCED -DTRILIBRARY -DANSI_DECLARATORS )
set_target_properties(cork PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(cork PROPERTIES PUBLIC_HEADER src/cork.h)

INSTALL(TARGETS cork EXPORT corkConfig
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/cork
)
INSTALL(EXPORT corkConfig
        NAMESPACE cork::
        DESTINATION share/cork/cmake)
