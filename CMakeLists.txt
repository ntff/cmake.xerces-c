cmake_minimum_required(VERSION 3.4)

project(xerces-c VERSION 3.1.3)

include(ExternalProject)

find_program(MAKE make)

ExternalProject_Add(${PROJECT_NAME}
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}
    URL http://www.apache.org/dist/xerces/c/${PROJECT_VERSION_MAJOR}/sources/xerces-c-${PROJECT_VERSION}.tar.gz
    URL_MD5 70320ab0e3269e47d978a6ca0c0e1e2d
    INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}
    CONFIGURE_COMMAND ./configure
	--prefix=<INSTALL_DIR>
	--disable-network
	--disable-shared
    BUILD_COMMAND  ${MAKE}
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND ${MAKE} install
)

ExternalProject_Get_Property(${PROJECT_NAME} INSTALL_DIR)

string(TOLOWER "${PROJECT_NAME}" PROJECT_NAME_LOWER)

configure_file(config.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config.cmake"
    @ONLY)

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config-version.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)


install(DIRECTORY
    "${INSTALL_DIR}/include"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

install(DIRECTORY
    "${INSTALL_DIR}/lib"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME_LOWER}-config-version.cmake"
    DESTINATION "lib/${CMAKE_LIBRARY_ARCHITECTURE}/${PROJECT_NAME_LOWER}-${PROJECT_VERSION}"
)

export(PACKAGE ${PROJECT_NAME})

set(CPACK_GENERATOR "TBZ2")
set(CPACK_PACKAGE_FILE_NAME "${PROJECT_NAME_LOWER}-${PROJECT_VERSION}-${CMAKE_LIBRARY_ARCHITECTURE}")
include(CPack)

if(CMAKE_CURRENT_BINARY_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    set_property(DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
        PROPERTY ADDITIONAL_MAKE_CLEAN_FILES
            include/
            lib/
            ${PROJECT_NAME}-config.cmake
            ${PROJECT_NAME}-config-version.cmake
            ${CPACK_PACKAGE_FILE_NAME}.tar.bz2
    )
endif()
