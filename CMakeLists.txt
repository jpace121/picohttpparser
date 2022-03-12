cmake_minimum_required(VERSION 3.5)
project(picohttpparser)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

if(CMAKE_COMPILER_IS_GNUCXX)
   add_compile_options(-Wno-psabi)
endif()

set(picohttpparser_MAJOR_VERSION 0)
set(picohttpparser_MINOR_VERSION 0)
set(picohttpparser_PATCH_VERSION 0)
set(picohttpparser_VERSION
  ${picohttpparser_MAJOR_VERSION}.${picohttpparser_MINOR_VERSION}.${picohttpparser_PATCH_VERSION})

add_library(picohttpparser SHARED picohttpparser.c)
target_include_directories(picohttpparser PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>)

# Causes the visibility macros to use dllexport rather than dllimport,
# which is appropriate when building the dll but not consuming it.
target_compile_definitions(picohttpparser PRIVATE "PICOHTTPPARSER_BUILDING_LIBRARY")

install(
  FILES picohttpparser.h
  DESTINATION include
)
install(
  TARGETS picohttpparser
  EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)
# export targets
set(export_targets ${export_targets};picohttpparser)
export(EXPORT export_picohttpparser
  FILE "${PROJECT_BINARY_DIR}/export_picohttpparser.cmake")

# Create the picohttpparserConfig.cmake
set(CONF_INCLUDE_DIRS "${CMAKE_INSTALL_PREFIX}/include")
configure_file(picohttpparserConfig.cmake.in
  "${PROJECT_BINARY_DIR}/picohttpparserConfig.cmake" @ONLY)

# Create the picohttpparserConfigVersion.cmake
configure_file(picohttpparserConfigVersion.cmake.in
  "${PROJECT_BINARY_DIR}/picohttpparserConfigVersion.cmake" @ONLY)

install(FILES
  "${PROJECT_BINARY_DIR}/${CMAKE_FILES_DIRECTORY}/picohttpparserConfig.cmake"
  "${PROJECT_BINARY_DIR}/picohttpparserConfigVersion.cmake"
  DESTINATION "share/${PROJECT_NAME}/cmake" COMPONENT dev)
install(EXPORT export_picohttpparser
  DESTINATION "share/${PROJECT_NAME}/cmake"
  FILE export_picohttpparser.cmake
  NAMESPACE "${PROJECT_NAME}::"
  COMPONENT dev)
