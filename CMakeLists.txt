cmake_minimum_required(VERSION 3.18)

project(gflip3d LANGUAGES CXX CUDA)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CUDA_STANDARD 17)
set(CMAKE_CUDA_STANDARD_REQUIRED ON)

# Set build type if not set
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# CUDA settings
find_package(CUDA REQUIRED)
set(CMAKE_CUDA_FLAGS "${CMAKE_CUDA_FLAGS} -use_fast_math")

# Add the source files
set(SOURCES
GDelFlipping/src/DelaunayChecker.cpp
GDelFlipping/src/Demo.cpp
GDelFlipping/src/InputCreator.cpp
GDelFlipping/src/RandGen.cpp
GDelFlipping/src/gDel3D/GpuDelaunay.cu
GDelFlipping/src/gDel3D/CPU/predicates.cpp
GDelFlipping/src/gDel3D/CPU/PredWrapper.cpp
GDelFlipping/src/gDel3D/CPU/Splaying.cpp
GDelFlipping/src/gDel3D/CPU/Star.cpp
GDelFlipping/src/gDel3D/GPU/KerDivision.cu
GDelFlipping/src/gDel3D/GPU/KerPredicates.cu
GDelFlipping/src/gDel3D/GPU/ThrustWrapper.cu
)

# Add the executable
add_executable(${PROJECT_NAME} ${SOURCES})

# Set CUDA properties for RTX 3070 (Ampere, compute capability 8.6)
set_target_properties(${PROJECT_NAME} PROPERTIES
    CUDA_SEPARABLE_COMPILATION ON
    CUDA_ARCHITECTURES 86
)

# Include directories
target_include_directories(${PROJECT_NAME} PRIVATE 
    ${CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES}
    ${CMAKE_CURRENT_SOURCE_DIR}/GDelFlipping/src
)


# Compiler options
target_compile_options(${PROJECT_NAME} PRIVATE
    $<$<COMPILE_LANGUAGE:CUDA>:
        --extended-lambda
        --expt-relaxed-constexpr
        -O3
    >
)

# Set output directories
set_target_properties(${PROJECT_NAME} PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)
