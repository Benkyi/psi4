find_package(pybind11 2.0.0 EXACT CONFIG QUIET)

if(${pybind11_FOUND})
    message(STATUS "${Cyan}Found pybind11${ColourReset}: ${pybind11_INCLUDE_DIR} (found version ${pybind11_VERSION})")
    add_library(pybind11_external INTERFACE)  # dummy
else()
    if(${CMAKE_INSIST_FIND_PACKAGE_pybind11})
        message(FATAL_ERROR "Suitable pybind11 could not be externally located as user insists")
    endif()

    include(ExternalProject)
    message(STATUS "Suitable pybind11 could not be located, ${Magenta}Building pybind11${ColourReset} instead.")
    ExternalProject_Add(pybind11_external
        GIT_REPOSITORY https://github.com/pybind/pybind11
        GIT_TAG v2.0.0
        UPDATE_COMMAND ""
        CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
                   -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}
                   -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}  # unused, but needs working compiler
                   -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}  # ditto
                   -DCMAKE_INSTALL_INCLUDEDIR=${CMAKE_INSTALL_INCLUDEDIR}
                   -DPYBIND11_TEST=OFF
        INSTALL_COMMAND DESTDIR=${CMAKE_BINARY_DIR}/stage ${CMAKE_MAKE_PROGRAM} install)

    set(pybind11_DIR ${STAGED_INSTALL_PREFIX}/share/cmake/pybind11 CACHE PATH "path to internally built pybind11Config.cmake" FORCE)
endif()

