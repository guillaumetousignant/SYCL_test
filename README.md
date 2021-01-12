# SYCL_test

Spectral elements method solver written with SYCL.

Installation:

```bash
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
cmake --install .
```

To build tests, add `-DBUILD_TESTING=ON` to cmake call. Then:

```bash
make unit_tests
ctest
```

To include in a CMake project:

```bash
find_package(SYCL_test 0.1.0 REQUIRED)
target_link_libraries(example SYCL_SEM::SYCL_SEM)
```
