# ########################################################################
# Copyright 2018 Advanced Micro Devices, Inc.
# ########################################################################

set(HIPSPARSE_CLIENTS_COMMON
  ../common/utility.cpp
)

# Function to add hipsparse examples
function(add_hipsparse_example EXAMPLE_SOURCE)
  get_filename_component(EXAMPLE_TARGET ${EXAMPLE_SOURCE} NAME_WE)
  add_executable(${EXAMPLE_TARGET} ${EXAMPLE_SOURCE} ${HIPSPARSE_CLIENTS_COMMON})

  target_include_directories(${EXAMPLE_TARGET} PRIVATE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>)

  set_target_properties(${EXAMPLE_TARGET} PROPERTIES CXX_STANDARD 11 CXX_STANDARD_REQUIRED ON)
  target_link_libraries(${EXAMPLE_TARGET} PRIVATE roc::hipsparse)

  if(NOT BUILD_CUDA)
    target_compile_definitions(${EXAMPLE_TARGET} PRIVATE __HIP_PLATFORM_HCC__)
    get_target_property(HIP_HCC_LOCATION hip::hip_hcc IMPORTED_LOCATION_RELEASE)
    get_target_property(HCC_AM_LOCATION hcc::hc_am IMPORTED_LOCATION_RELEASE)
    target_link_libraries(${EXAMPLE_TARGET} PRIVATE ${HIP_HCC_LOCATION} ${HCC_AM_LOCATION})
  else()
    target_compile_definitions(${EXAMPLE_TARGET} PRIVATE __HIP_PLATFORM_NVCC__)
    target_link_libraries(${EXAMPLE_TARGET} PRIVATE ${CUDA_LIBRARIES})
  endif()

  set_target_properties(${EXAMPLE_TARGET} 
    PROPERTIES
      RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/example"
  )
endfunction()

# Examples
add_hipsparse_example(example_handle.cpp)
add_hipsparse_example(example_coomv.cpp)
add_hipsparse_example(example_csrmv.cpp)
add_hipsparse_example(example_ellmv.cpp)
add_hipsparse_example(example_hybmv.cpp)