cmake_minimum_required(VERSION 3.21)
project(xkelib)

set(XKELIB_DEFS
    kernel/kernelext21256.0.def
    xam/xamext21256.0.def
)

set(XKELIB_OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/xkelib.lib")

# The XDK lib.exe needs /def: for each def file.
add_custom_command(
    OUTPUT "${XKELIB_OUTPUT}"
    COMMAND "${CMAKE_AR}" /nologo /MACHINE:PPCBE "/OUT:${XKELIB_OUTPUT}" "/def:${CMAKE_CURRENT_SOURCE_DIR}/kernel/kernelext21256.0.def" "/def:${CMAKE_CURRENT_SOURCE_DIR}/xam/xamext21256.0.def"
    DEPENDS ${XKELIB_DEFS}
    WORKING_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}"
    COMMENT "Generating xkelib.lib from .def files"
    VERBATIM
)

# We use a custom target to drive the generation.
add_custom_target(xkelib_gen DEPENDS "${XKELIB_OUTPUT}")

# Create an imported target for the library.
add_library(xkelib STATIC IMPORTED GLOBAL)
add_dependencies(xkelib xkelib_gen)
set_target_properties(xkelib PROPERTIES
    IMPORTED_LOCATION "${XKELIB_OUTPUT}"
    INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}"
    INTERFACE_COMPILE_DEFINITIONS "_XBOX;NDEBUG;_LIB"
)
