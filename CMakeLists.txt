# CMake for AntMediaGstreamerWebRTC

cmake_minimum_required(VERSION 3.29)

cmake_policy(SET CMP0048 NEW)

project(
  AntMediaGstreamerWebRTC
  VERSION 1.0
  LANGUAGES C)

# If you set any CMAKE_ variables, that can go here.
# (But usually don't do this, except maybe for C++ standard)

find_package(PkgConfig REQUIRED)
pkg_check_modules(GSTLIBS REQUIRED
        gobject-2.0
        glib-2.0
        gstreamer-webrtc-1.0
        gstreamer-sdp-1.0
        gstreamer-pbutils-1.0
        json-glib-1.0)

pkg_check_modules(SOUP3 REQUIRED libsoup-3.0)

include_directories(
        ${GSTLIBS_INCLUDE_DIRS}
        ${SOUP3_INCLUDE_DIRS})

link_directories(${SOUP3_LIBRARY_DIRS})

add_executable(sendRecvAnt sendRecvAnt.c)
target_link_options(sendRecvAnt PRIVATE "LINKER:--copy-dt-needed-entries")
target_link_libraries(sendRecvAnt PRIVATE ${GSTLIBS_LIBRARIES} ${SOUP3_LIBRARIES})

install(TARGETS sendRecvAnt DESTINATION bin)
