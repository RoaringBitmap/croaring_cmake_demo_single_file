# Copy this file as CMakeLists.txt in
# some directory. With a recent CMake
# (at least 3.15), in this directory do
#
# cmake -B build .
# cmake --build build
# ./build/repro
#
#
cmake_minimum_required(VERSION 3.15)


project(CrashRepro VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

include(FetchContent)

FetchContent_Declare(
  roaring
  GIT_REPOSITORY https://github.com/RoaringBitmap/CRoaring.git
  GIT_TAG v2.0.4
  GIT_SHALLOW TRUE)

set(ENABLE_ROARING_TESTS OFF CACHE INTERNAL "")

set(ROARING_BUILD_STATIC ON CACHE INTERNAL "")
FetchContent_MakeAvailable(roaring)

FetchContent_GetProperties(roaring)
SET(CPP_ROARING_HEADERS ${roaring_SOURCE_DIR}/cpp/roaring64map.hh  ${roaring_SOURCE_DIR}/cpp/roaring.hh)
file(COPY  ${CPP_ROARING_HEADERS} DESTINATION ${roaring_SOURCE_DIR}/include/roaring)


file(WRITE main.cpp "
#include <iostream>
#include \"roaring/roaring.hh\"
int main() {
  roaring::Roaring r1;
  for (uint32_t i = 100; i < 1000; i++) {
    r1.add(i);
  }
  std::cout << \"cardinality = \" << r1.cardinality() << std::endl;

  return 0;
}")



add_executable(repro main.cpp)
target_link_libraries(repro PUBLIC roaring)


file(WRITE main.c "
#include <stdio.h>
#include \"roaring/roaring.h\"
int main() {
  roaring_bitmap_t *r1 = roaring_bitmap_create();
  for (uint32_t i = 100; i < 1000; i++) roaring_bitmap_add(r1, i);
  printf(\"cardinality = %d\\n\", (int) roaring_bitmap_get_cardinality(r1));
  roaring_bitmap_free(r1);
  return 0;
}")



add_executable(reproc main.c)
target_link_libraries(reproc PUBLIC roaring)
