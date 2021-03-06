cmake_minimum_required(VERSION 3.4.1)

set(local_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src/main/cpp)
set(library_JniHelpers_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../library/src/main/cpp)
set(library_JniHelpersTest_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../library/src/androidTest/cpp)

file(GLOB libLocal_SOURCES ${local_DIR}/*.cpp)
file(GLOB libLocal_HEADERS ${local_DIR}/*.h)

file(GLOB libJniHelpers_SOURCES ${library_JniHelpers_DIR}/*.cpp)
file(GLOB libJniHelpers_HEADERS ${library_JniHelpers_DIR}/*.h)

file(GLOB libJniHelpersTest_SOURCES ${library_JniHelpersTest_DIR}/*.cpp)
file(GLOB libJniHelpersTest_HEADERS ${library_JniHelpersTest_DIR}/*.h)

link_directories(${JNI_LIBRARIES})
add_library(JniHelpers STATIC ${libLocal_SOURCES} ${libJniHelpers_SOURCES})
add_library(JniHelpersTest STATIC ${libLocal_SOURCES} ${libJniHelpers_SOURCES} ${libJniHelpersTest_SOURCES})
include_directories(${local_DIR} ${library_JniHelpers_DIR} ${library_JniHelpersTest_DIR})


if(${ANDROID_TESTING})
    set(library_NAME "test-lib")
    set(library_Entry_Point "src/androidTest/cpp")
else()
    set(library_NAME "native-lib")
    set(library_Entry_Point "src/main/cpp")
endif()

add_library( # Sets the name of the library.
             ${library_NAME}

             # Sets the library as a shared library.
             SHARED

             # The following toggles between the native-lib and test-libs
             # files, based upon whehther a test is currently running.
             ${library_Entry_Point}/${library_NAME}.cpp )


find_library( # Sets the name of the path variable.
              log-lib

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log )


# Only adding JniHelpersTest library if testing
if(${ANDROID_TESTING})
    target_link_libraries(${library_NAME} JniHelpers JniHelpersTest ${log-lib} )
else()
    target_link_libraries(${library_NAME} JniHelpers ${log-lib} )
endif()