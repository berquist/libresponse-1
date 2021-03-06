cmake_minimum_required(VERSION 3.0)
project(LIBRESPONSE CXX)

find_package(Armadillo)

set(LIBRESPONSE_UNPREFIXED_SOURCES
    citations.C
    configurable.C
    dump_ao_integrals.C
    index_printing.C
    indices.C
    matvec_i.C
    utils.C
    linear/helpers.C
    linear/interface.C
    linear/interface_nonorthogonal.C
    linear/printing.C
    operator_spec.C
    set_defaults.C
    )
include(cmake/functions.cmake)
prepend(LIBRESPONSE_SOURCES "src/libresponse" "${LIBRESPONSE_UNPREFIXED_SOURCES}")

add_library(response STATIC ${LIBRESPONSE_SOURCES})
target_include_directories(response PUBLIC "${ARMADILLO_INCLUDE_DIRS}")
target_link_libraries(response "${ARMADILLO_LIBRARIES}")

if(OPENMP_FOUND)
    set_target_properties(response PROPERTIES COMPILE_FLAGS "${OpenMP_CXX_FLAGS}")
endif(OPENMP_FOUND)

find_package(Doxygen 1.8)
if(DOXYGEN_FOUND)
    configure_file(
        "${CMAKE_CURRENT_SOURCE_DIR}/docs/Doxyfile.in"
        "${CMAKE_CURRENT_BINARY_DIR}/Doxyfile" @ONLY)
    add_custom_target(response_docs
        "${DOXYGEN_EXECUTABLE}" "${CMAKE_CURRENT_BINARY_DIR}/Doxyfile"
        WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
        COMMENT "Doxygen documentation" VERBATIM)
endif(DOXYGEN_FOUND)
