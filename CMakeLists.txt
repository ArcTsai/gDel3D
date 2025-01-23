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

# Add Thrust dependencies
target_link_libraries(${PROJECT_NAME} PRIVATE 
    CUDA::cudart
    CUDA::cuda_driver
    ${CUDA_LIBRARIES}
    ${CUDA_cublas_LIBRARY}
    ${CUDA_cublasLt_LIBRARY}
    ${CUDA_cudadevrt_LIBRARY}
    ${CUDA_cudart_static_LIBRARY}
    ${CUDA_cufft_LIBRARY}
    ${CUDA_cufftw_LIBRARY}
    ${CUDA_curand_LIBRARY}
    ${CUDA_cusolver_LIBRARY}
    ${CUDA_cusolverMg_LIBRARY}
    ${CUDA_cusparse_LIBRARY}
    ${CUDA_nppc_LIBRARY}
    ${CUDA_nppial_LIBRARY}
    ${CUDA_nppicc_LIBRARY}
    ${CUDA_nppidei_LIBRARY}
    ${CUDA_nppif_LIBRARY}
    ${CUDA_nppig_LIBRARY}
    ${CUDA_nppim_LIBRARY}
    ${CUDA_nppist_LIBRARY}
    ${CUDA_nppisu_LIBRARY}
    ${CUDA_nppitc_LIBRARY}
    ${CUDA_npps_LIBRARY}
    ${CUDA_nvblas_LIBRARY}
    ${CUDA_nvJitLink_LIBRARY}
    ${CUDA_nvjpeg_LIBRARY}
    ${CUDA_nvrtc_LIBRARY}
    ${CUDA_OpenCL_LIBRARY}
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
