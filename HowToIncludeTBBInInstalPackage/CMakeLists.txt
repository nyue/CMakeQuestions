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

if (WIN32)
  
  include (InstallRequiredSystemLibraries)

  install(TARGETS tbbi
	DESTINATION bin
	RUNTIME_DEPENDENCIES
	PRE_EXCLUDE_REGEXES "api-ms-" "ext-ms-"
	POST_EXCLUDE_REGEXES ".*system32/.*\\dll"
	DIRECTORIES $<TARGET_FILE_DIR:TBB::tbb>)
  
else(WIN32)
  
  install(TARGETS
	tbbi
	DESTINATION
	bin)
  
  install(FILES $<TARGET_FILE:TBB::tbb> $<TARGET_SONAME_FILE:TBB::tbb> DESTINATION lib)
  
endif(WIN32)

set(CPACK_PACKAGE_NAME "MyTBB")
set(CPACK_PACKAGE_VENDOR "MyCompany.org")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY
    "MyTBB - TBB dependencies example")
set(CPACK_PACKAGE_VERSION "0.0.1")
set(CPACK_PACKAGE_VERSION_MAJOR "0")
set(CPACK_PACKAGE_VERSION_MINOR "0")
set(CPACK_PACKAGE_VERSION_PATCH "1")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "MyTBB-${CPACK_PACKAGE_VERSION}")

include(CPack)
