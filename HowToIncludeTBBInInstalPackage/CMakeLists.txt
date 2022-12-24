cmake_minimum_required(VERSION 3.25)

project(TBBInclusion)

find_package(TBB 2020 REQUIRED
  )

# see : https://dev.my-gate.net/2021/08/04/understanding-rpath-with-cmake/
# use, i.e. don't skip the full RPATH for the build tree
set(CMAKE_SKIP_BUILD_RPATH FALSE)
 
# when building, don't use the install RPATH already
# (but later on when installing)
set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
 
# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

add_executable(tbbi
  main.cpp)

target_link_libraries(tbbi
  TBB::tbb)

get_target_property(TBB_tbb_shared_libraries TBB::tbb IMPORT_LOCATION)

message("TBB_tbb_shared_libraries = ${TBB_tbb_shared_libraries}")

install(TARGETS
  tbbi
  DESTINATION
  bin)

foreach(_shared_lib ${TBB_tbb_shared_libraries})
  install(FILES
	${_shared_lib}
	DESTINATION
	lib)
endforeach()

