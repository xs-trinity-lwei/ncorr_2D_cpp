cmake_minimum_required(VERSION 3.15)
project(ncorr_library LANGUAGES CXX)

# Source files
set(ncorr_src
    ../src/ncorr.cpp
    ../src/Strain2D.cpp
    ../src/Disp2D.cpp
    ../src/Data2D.cpp
    ../src/ROI2D.cpp
    ../src/Image2D.cpp
    ../src/Array2D.cpp
)

set(ncorr_h
    ../include/ncorr.h
    ../include/Strain2D.h
    ../include/Disp2D.h
    ../include/Data2D.h
    ../include/ROI2D.h
    ../include/Image2D.h
    ../include/Array2D.h
)

add_library(ncorr STATIC ${ncorr_src})

# Public include path for consumers
# (SuiteSparse headers are added later as PRIVATE includes)
target_include_directories(ncorr
    PUBLIC
        ../include
)

set_target_properties(ncorr PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED ON
    CXX_EXTENSIONS OFF
)

# Output directory
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ../lib)

# Compiler options
if(MSVC)
    target_compile_definitions(ncorr PRIVATE
        NDEBUG
        NOMINMAX
        _CRT_SECURE_NO_WARNINGS
        _USE_MATH_DEFINES
    )

    target_compile_options(ncorr PRIVATE
        /O2
        /MP
        /permissive-
        /Zc:__cplusplus
    )
else()
    include(CheckCXXCompilerFlag)
    check_cxx_compiler_flag("-O3" COMPILER_SUPPORTS_O3)
    if(COMPILER_SUPPORTS_O3)
        target_compile_options(ncorr PRIVATE -O3)
    endif()
    target_compile_definitions(ncorr PRIVATE NDEBUG)
endif()

# -------------------------
# Dependencies
# -------------------------
# OpenCV
find_package(OpenCV REQUIRED COMPONENTS core imgproc highgui imgcodecs)
target_link_libraries(ncorr PRIVATE ${OpenCV_LIBS})
target_include_directories(ncorr PRIVATE ${OpenCV_INCLUDE_DIRS})

# FFTW3
find_package(FFTW3 CONFIG QUIET)
if(FFTW3_FOUND)
    if(TARGET FFTW3::fftw3)
        target_link_libraries(ncorr PRIVATE FFTW3::fftw3)
    elseif(TARGET fftw3)
        target_link_libraries(ncorr PRIVATE fftw3)
    endif()
else()
    find_path(FFTW3_INCLUDE_DIR fftw3.h)
    find_library(FFTW3_LIBRARY NAMES fftw3 libfftw3-3 fftw3-3)

    if(NOT FFTW3_INCLUDE_DIR OR NOT FFTW3_LIBRARY)
        message(FATAL_ERROR "FFTW3 not found. Install FFTW3 (for example via vcpkg: fftw3).")
    endif()

    target_include_directories(ncorr PRIVATE ${FFTW3_INCLUDE_DIR})
    target_link_libraries(ncorr PRIVATE ${FFTW3_LIBRARY})
endif()

# SuiteSparse / SPQR (required by Data2D.cpp)
find_path(SUITESPARSE_INCLUDE_DIR SuiteSparseQR.hpp)
find_library(SPQR_LIBRARY NAMES spqr libspqr)
find_library(CHOLMOD_LIBRARY NAMES cholmod libcholmod)
find_library(AMD_LIBRARY NAMES amd libamd)
find_library(COLAMD_LIBRARY NAMES colamd libcolamd)
find_library(SUITESPARSECONFIG_LIBRARY NAMES suitesparseconfig libsuitesparseconfig)

if(
    NOT SUITESPARSE_INCLUDE_DIR OR
    NOT SPQR_LIBRARY OR
    NOT CHOLMOD_LIBRARY OR
    NOT AMD_LIBRARY OR
    NOT COLAMD_LIBRARY OR
    NOT SUITESPARSECONFIG_LIBRARY
)
    message(FATAL_ERROR
        "SuiteSparse (SPQR/CHOLMOD/AMD/COLAMD/suitesparseconfig) not found. "
        "Install suitesparse (for example via vcpkg: suitesparse)."
    )
endif()

target_include_directories(ncorr PRIVATE ${SUITESPARSE_INCLUDE_DIR})
target_link_libraries(ncorr PRIVATE
    ${SPQR_LIBRARY}
    ${CHOLMOD_LIBRARY}
    ${AMD_LIBRARY}
    ${COLAMD_LIBRARY}
    ${SUITESPARSECONFIG_LIBRARY}
)

# Install library and public headers
install(TARGETS ncorr DESTINATION lib)
install(FILES ${ncorr_h} DESTINATION include)
