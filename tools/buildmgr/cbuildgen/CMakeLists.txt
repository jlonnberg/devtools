SET(LIB_SOURCES BuildSystemGenerator.cpp CbuildGen.cpp CMakeListsGenerator.cpp AuxCmd.cpp)
SET(LIB_HEADER BuildSystemGenerator.h CbuildGen.h CMakeListsGenerator.h AuxCmd.h ProductInfo.h)
SET(SOURCE_FILES Console.cpp)
SET(HEADER_FILES Resource.h cbuildgen.rc)

list(TRANSFORM SOURCE_FILES PREPEND src/)
list(TRANSFORM HEADER_FILES PREPEND include/)
list(TRANSFORM LIB_SOURCES PREPEND src/)
list(TRANSFORM LIB_HEADER PREPEND include/)

# lib target
add_library(cbuildgenlib OBJECT ${LIB_SOURCES} ${LIB_HEADER})
target_link_libraries(cbuildgenlib PUBLIC cxxopts cbuild)
target_include_directories(cbuildgenlib
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/include ${PROJECT_BINARY_DIR})

if(MSVC)
  target_link_options(cbuildgenlib PUBLIC "$<$<CONFIG:Debug>:/SAFESEH:NO>")
endif(MSVC)

set_property(TARGET cbuildgenlib PROPERTY
  MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

# cbuildgen target
add_executable(cbuildgen ${SOURCE_FILES} ${HEADER_FILES})

if(MSVC)
  target_link_options(cbuildgen PUBLIC "$<$<CONFIG:Debug>:/SAFESEH:NO>")
endif(MSVC)
set_property(TARGET cbuildgen PROPERTY
  MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR
   "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  target_link_options(cbuildgen PUBLIC "-static")
endif()

target_include_directories(cbuildgen
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
  PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/include)

target_link_libraries(cbuildgen PUBLIC cbuildgenlib)
