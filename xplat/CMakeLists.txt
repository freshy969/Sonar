cmake_minimum_required (VERSION 3.6.0)
project(sonarcpp CXX)
set(CMAKE_VERBOSE_MAKEFILE on)
set(PACKAGE_NAME      sonarcpp)

set(third_party_ndk ../android/build/third-party-ndk)
set(libfolly_DIR ${third_party_ndk}/folly/)
set(easywsclient_DIR ../libs/)
set(glog_DIR ${third_party_ndk}/glog)
set(BOOST_DIR ${third_party_ndk}/boost/boost_1_63_0/)

set(DOUBLECONVERSION_DIR ${third_party_ndk}/double-conversion/double-conversion-3.0.0/)

list(APPEND dir_list ./)
list(APPEND dir_list ./Sonar)
include_directories(${dir_list})

add_compile_options(-DFOLLY_NO_CONFIG
                    -DFB_SONARKIT_ENABLED=1
                    -DFOLLY_HAVE_MEMRCHR
                    -DFOLLY_MOBILE=1
                    -DFOLLY_USE_LIBCPP=1
                    -DFOLLY_HAVE_LIBJEMALLOC=0
                    -DFOLLY_HAVE_PREADV=0
                    -frtti
                    -fexceptions
                    -std=c++14
                    -Wno-error
                    -Wno-unused-local-typedefs
                    -Wno-unused-variable
                    -Wno-sign-compare
                    -Wno-comment
                    -Wno-return-type
                    -Wno-tautological-constant-compare
                    )


file(GLOB SOURCES Sonar/*.cpp)
add_library(${PACKAGE_NAME} SHARED ${SOURCES})

set(build_DIR ${CMAKE_SOURCE_DIR}/build)
set(libfolly_build_DIR ${build_DIR}/libfolly/${ANDROID_ABI})
set(easywsclient_build_DIR ${build_DIR}/easywsclient/${ANDROID_ABI})

file(MAKE_DIRECTORY ${build_DIR})

add_subdirectory(${libfolly_DIR} ${libfolly_build_DIR})
add_subdirectory(${easywsclient_DIR}/easywsclient ${easywsclient_build_DIR})

target_include_directories(${PACKAGE_NAME} PRIVATE
        ${libfolly_DIR}
        ${BOOST_DIR}
        ${BOOST_DIR}/../
        ${glog_DIR}
        ${glog_DIR}/../
        ${glog_DIR}/glog-0.3.5/src/
        ${easywsclient_DIR}
    )

target_link_libraries(${PACKAGE_NAME}  folly easywsclient glog double-conversion log)
