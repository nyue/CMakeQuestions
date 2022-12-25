cmake_minimum_required(VERSION 3.25)

project(TBBInclusion)

find_package(TBB 2020 REQUIRED
  )

# see : https://dev.my-gate.net/2021/08/04/understanding-rpath-with-cmake/
# see also : https://stackoverflow.com/questions/2836330/is-there-a-programmatic-way-to-inspect-the-current-rpath-on-linux
# use, i.e. don't skip the full RPATH for the build tree
set(CMAKE_SKIP_BUILD_RPATH FALSE)
 
# when building, don't use the install RPATH already
# (but later on when installing)
set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
# Nicholas commented this out - we need relative path because executable is in bin while the shared libraries are in lib
# set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
set(CMAKE_INSTALL_RPATH "$ORIGIN/../lib")
 
# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
# Nicholas commented this out - it adds the internal path to the RPATH which may contains pathname which might be a security risk
# set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

add_executable(tbbi
  main.cpp)

target_link_libraries(tbbi
  PUBLIC
  TBB::tbb)

install(TARGETS
  tbbi
  DESTINATION
  bin)

install(FILES $<TARGET_FILE:TBB::tbb> $<TARGET_SONAME_FILE:TBB::tbb> DESTINATION lib)

include(CPack)
