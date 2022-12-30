#
# To build it with cmake, you should register qml types explicitly by calling registerQuickFluxQmlTypes() in your main.cpp
# See examples/middleware for example
#



cmake_minimum_required(VERSION 3.14)



set(${PROJECT_NAME}_MAJOR_VERSION 1)
set(${PROJECT_NAME}_MINOR_VERSION 5)
set(${PROJECT_NAME}_PATCH_VERSION 0)
set(${PROJECT_NAME}_VERSION
    ${${PROJECT_NAME}_MAJOR_VERSION}.${${PROJECT_NAME}_MINOR_VERSION}.${${PROJECT_NAME}_PATCH_VERSION}
)
project(
  QuickPromise
  LANGUAGES CXX
  VERSION ${${PROJECT_NAME}_VERSION})


set(CMAKE_AUTOMOC ON)

find_package(
  QT NAMES Qt6 Qt5
  COMPONENTS Core Qml 
  REQUIRED)

find_package(
  Qt${QT_VERSION_MAJOR}
  COMPONENTS Core Qml
  REQUIRED)

set(SRC_FILES
    ${CMAKE_CURRENT_SOURCE_DIR}/qml/quickpromise.qrc
    )

add_library(${PROJECT_NAME} STATIC ${SRC_FILES})

target_link_libraries(
  ${PROJECT_NAME} PUBLIC Qt${QT_VERSION_MAJOR}::Core Qt${QT_VERSION_MAJOR}::Qml)

set_target_properties(${PROJECT_NAME} PROPERTIES DEBUG_POSTFIX d)

include(cmake/CompilerOptionsAndWarningsConfig.cmake)
set_project_options_and_warnings(${PROJECT_NAME})

#Install script
option(${PROJECT_NAME}_INSTALL "Enable Installing as Library" ON)

if(${PROJECT_NAME}_INSTALL)
  # Offer the user the choice of overriding the installation directories
  install(
    TARGETS ${PROJECT_NAME}
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
    EXPORT ${PROJECT_NAME}Targets)

  install(FILES ${INCLUDE_FILES} DESTINATION include/${PROJECT_NAME})

  include(CMakePackageConfigHelpers)
  write_basic_package_version_file(
    ${CMAKE_BINARY_DIR}/cmake/${PROJECT_NAME}Version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion)

  # installation - build tree specific package config files
  export(EXPORT ${PROJECT_NAME}Targets
         FILE ${CMAKE_BINARY_DIR}/${PROJECT_NAME}Targets.cmake)
  # installation - relocatable package config files
  configure_package_config_file(
    ${PROJECT_SOURCE_DIR}/${PROJECT_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}Config.cmake
    INSTALL_DESTINATION cmake)

  set(CONFIG_PACKAGE_LOCATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME})

  install(
    EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CONFIG_PACKAGE_LOCATION})

  install(FILES ${CMAKE_BINARY_DIR}/cmake/${PROJECT_NAME}Config.cmake
                ${CMAKE_BINARY_DIR}/cmake/${PROJECT_NAME}Version.cmake
          DESTINATION ${CONFIG_PACKAGE_LOCATION})
endif()
