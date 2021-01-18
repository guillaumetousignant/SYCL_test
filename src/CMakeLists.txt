add_library(SYCL_SEM noot.cpp)

target_include_directories(SYCL_SEM PUBLIC 
    $<INSTALL_INTERFACE:include/SYCL_SEM/>    
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include/>)

find_package(OpenMP)

if(OpenMP_CXX_FOUND)
    target_link_libraries(SYCL_SEM PUBLIC optimized OpenMP::OpenMP_CXX)
endif()

if(NOT WIN32)
    target_link_libraries(SYCL_SEM PUBLIC debug pthread)
endif()

target_compile_features(SYCL_SEM PRIVATE cxx_std_20)
if(NOT ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug"))
    set_property(TARGET SYCL_SEM PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
endif()

install(TARGETS SYCL_SEM 
    EXPORT sycl_fem-export
    DESTINATION lib)

if(NOT CMAKE_SOURCE_DIR STREQUAL CMAKE_INSTALL_PREFIX)
    install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/ 
        DESTINATION include/SYCL_SEM
        FILES_MATCHING PATTERN "*.h")
endif()

if(WIN32)
    install(DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/
        DESTINATION bin
        FILES_MATCHING PATTERN "*.dll")
endif()

install(EXPORT sycl_fem-export
  FILE
  SYCL_SEMConfig.cmake
  NAMESPACE
  SYCL_SEM::
  DESTINATION
    lib/cmake/SYCL_SEM
)

add_executable(sycl_test ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp)

if(WIN32)
    set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake/)
    include(FindComputeCpp)
    include_directories(${COMPUTECPP_INCLUDE_DIRECTORY})
else()   
    set(HIPSYCL_TARGETS "cuda:sm_60") # sm_60 is Pascal
    find_package(hipSYCL)
endif()

add_sycl_to_target(TARGET sycl_test SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
                        ${CMAKE_CURRENT_BINARY_DIR})

target_link_libraries(sycl_test PRIVATE SYCL_SEM)
target_compile_features(sycl_test PRIVATE cxx_std_20)
if(NOT ("${CMAKE_BUILD_TYPE}" STREQUAL "Debug"))
    set_property(TARGET sycl_test PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
endif()

install(TARGETS sycl_test DESTINATION bin)
