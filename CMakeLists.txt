cmake_minimum_required (VERSION 3.13)

# use project VERSION
cmake_policy(SET CMP0048 NEW)

project(smvs LANGUAGES CXX VERSION 1.0.0)

option(SMVS_BUILD_APP "Build the applications" ON)
option(SMVS_BUILD_TOOLS "Build the tools" ON)
option(SMVS_BUILD_TESTS "Build the tests" ON)
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

include(GNUInstallDirs)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR}")

find_package(JPEG REQUIRED)
find_package(PNG REQUIRED)
find_package(TIFF REQUIRED)
find_package(Threads REQUIRED)
find_package(mve REQUIRED)


# Layout. This works for all platforms:
#   * <prefix>/lib/cmake/<PROJECT-NAME>
#   * <prefix>/lib/
#   * <prefix>/include/
set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(include_install_dir "${CMAKE_INSTALL_INCLUDEDIR}")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")


add_subdirectory(lib)
if(SMVS_BUILD_APP)
    add_subdirectory(app)
endif()
if(SMVS_BUILD_TOOLS)
    add_subdirectory(tools)
endif()
if(SMVS_BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

# Include module with function 'write_basic_package_version_file'
include(CMakePackageConfigHelpers)

# Configure '<PROJECT-NAME>ConfigVersion.cmake'
# Note: PROJECT_VERSION is used as a VERSION
write_basic_package_version_file(
        "${version_config}" COMPATIBILITY SameMajorVersion
)

# Configure '<PROJECT-NAME>Config.cmake'
# Use variables:
#   * targets_export_name
#   * PROJECT_NAME
configure_package_config_file(
        "cmake/Config.cmake.in"
        "${project_config}"
        INSTALL_DESTINATION "${config_install_dir}"
)

export(EXPORT ${targets_export_name} FILE "${generated_dir}/${targets_export_name}.cmake")

# Config
#   * <prefix>/lib/cmake/smvs/smvsConfig.cmake
#   * <prefix>/lib/cmake/smvs/smvsConfigVersion.cmake
install(
        FILES "${project_config}" "${version_config}"
        DESTINATION "${config_install_dir}"
)

# Config
#   * <prefix>/lib/cmake/smvs/smvsTargets.cmake
install(
        EXPORT ${targets_export_name}
        NAMESPACE "${namespace}"
        DESTINATION "${config_install_dir}"
)