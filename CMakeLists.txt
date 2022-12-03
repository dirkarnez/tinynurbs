cmake_minimum_required(VERSION 3.5)

# temp
set(version 1.0.0)

project(tinynurbs VERSION ${version} LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 14)
# set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/")

set(BUILD_TESTS ON CACHE BOOL "Build unit tests")
# message(STATUS "Variable from cache: ${GLM_ROOT_DIR}")

find_package(glm 0.9.9 REQUIRED)

set(HEADER_FILES 
    include/tinynurbs/tinynurbs.h
    include/tinynurbs/core/basis.h
    include/tinynurbs/core/check.h
    include/tinynurbs/core/curve.h
    include/tinynurbs/core/evaluate.h
    include/tinynurbs/core/modify.h
    include/tinynurbs/core/surface.h
    include/tinynurbs/io/obj.h
    include/tinynurbs/util/util.h
    include/tinynurbs/util/array2.h
)
source_group("Header Files" FILES ${HEADER_FILES})
source_group("CMake Files" FILES CMakeLists.txt)

add_library(tinynurbs INTERFACE)
add_library(tinynurbs::tinynurbs ALIAS tinynurbs)

include(GNUInstallDirs)

target_link_libraries(tinynurbs INTERFACE glm)

target_include_directories(tinynurbs INTERFACE 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

install(TARGETS tinynurbs EXPORT tinynurbs)

add_custom_target(tinynurbs_dummy SOURCES ${HEADER_FILES} CMakeLists.txt)

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
    include(CPack)
    
	install(DIRECTORY "include/tinynurbs" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}" PATTERN "CMakeLists.txt" EXCLUDE)

    install(EXPORT tinynurbs FILE tinynurbsConfig.cmake DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/tinynurbs" NAMESPACE tinynurbs::)

    include(CMakePackageConfigHelpers)

    # # install the configuration files that we using the helper macros above:
    # install(
    #     FILES
    #     "${CMAKE_CURRENT_BINARY_DIR}/tinynurbsConfig.cmake" # cmake config file
    #     "${CMAKE_CURRENT_BINARY_DIR}/tinynurbsConfigVersion.cmake" # cmake version file
    #     DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/tinynurbs
    # )

    # generate the version file for the config file
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/tinynurbsConfigVersion.cmake"
        VERSION ${version}
        COMPATIBILITY AnyNewerVersion # probably don't want to set this. Would need to check other options.
    )

    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/tinynurbsConfigVersion.cmake DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/tinynurbs)


    if(BUILD_TESTS)
        enable_testing()
        add_subdirectory(tests)
    endif()
endif(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})


