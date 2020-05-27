cmake_minimum_required(VERSION 3.0)

set (my_project "IO-Benchmarks-C++")

project(my_project)

SET(CMAKE_CXX_STANDARD 14)
SET(CMAKE_CXX_STANDARD_REQUIRED TRUE)

#this alows use to run the executables made after the `make install` command is executed by the user
set(CMAKE_MACOSX_RPATH 1)
# the RPATH to be used when installing
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")

add_subdirectory(tests)

add_library(io_perf SHARED ${CMAKE_CURRENT_SOURCE_DIR}/src/IO_perf.cc)

target_include_directories(io_perf PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/)

install(TARGETS io_perf LIBRARY DESTINATION lib)

##################
#in another folder
##################

add_executable(io io.cc)

target_link_libraries(io LINK_PUBLIC io_perf)

install(DIRECTORY DESTINATION "output")

install(TARGETS io RUNTIME DESTINATION bin)

##################
#in another folder
##################

cmake_minimum_required(VERSION 3.0)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

set(CMAKE_MACOSX_RPATH OFF)
# set(CMAKE_INSTALL_RPATH_USE_LINK_PATH ON)

set(project_name "RANDOM-GENERATOR-PIPELINE")

project(${project_name})

# set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

# this creates a folder 
add_custom_target(add_folder ALL COMMAND ${CMAKE_COMMAND} -E make_directory "output")

#this alows use to run the executables made after the `make install` command is executed by the user
set(CMAKE_MACOSX_RPATH 1)
# the RPATH to be used when installing
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")

# don't add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
# set(CMAKE_INSTALL_RPATH_USE_LINK_PATH FALSE)


#move the libraries to the bin folder 
# set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/tarball/build/bin)

# set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

#this creates a copy command for any folder
add_custom_target(keep_scripts ALL
        COMMAND ${CMAKE_COMMAND} -E copy_directory
                ${CMAKE_SOURCE_DIR}/scripts
                ${CMAKE_CURRENT_BINARY_DIR}/scripts)

add_subdirectory(tests)

add_library(container SHARED ${CMAKE_CURRENT_SOURCE_DIR}/src/container.cc)
add_library(twister SHARED ${CMAKE_CURRENT_SOURCE_DIR}/src/twister.cc)
add_library(filestream SHARED ${CMAKE_CURRENT_SOURCE_DIR}/src/filestream.cc)

target_link_libraries(filestream LINK_PUBLIC container)
target_link_libraries(filestream LINK_PUBLIC twister)
target_link_libraries(container LINK_PUBLIC twister)

# DEBIAN DOESNT NEED FULL PATH FOR INCLUDE
target_include_directories(container PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_include_directories(twister PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_include_directories(filestream PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
# target_include_directories(container PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/container.hh)
# target_include_directories(twister PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/twister..hh)
# target_include_directories(filestream PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include/filestream.hh)

install(TARGETS container LIBRARY DESTINATION lib)
install(TARGETS twister LIBRARY DESTINATION lib)
install(TARGETS filestream LIBRARY DESTINATION lib)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/scripts/clean_files.sh DESTINATION ${CMAKE_INSTALL_PREFIX}/scripts)