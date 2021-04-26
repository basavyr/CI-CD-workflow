# main file

cmake_minimum_required(VERSION 3.0)

set(project_name "HISTOGRAM-MAKER")

set(py_dir "python-bindings")

set(pkgs "${CMAKE_SOURCE_DIR}/packages.json")

project(${project_name})

SET(CMAKE_CXX_STANDARD 14)
SET(CMAKE_CXX_STANDARD_REQUIRED TRUE)

#this alows use to run the executables made after the `make install` command is executed by the user
set(CMAKE_MACOSX_RPATH 1)
# the RPATH to be used when installing
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")

# use, i.e. don't skip the full RPATH for the build tree
set(CMAKE_SKIP_BUILD_RPATH FALSE)

# when building, don't use the install RPATH already
# (but later on when installing)
set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)

# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

find_package (Python3 COMPONENTS Interpreter Development) # look for python3
# find_package (Python2 COMPONENTS Interpreter Development)#  look for python2

#installs the missing packages on the platform

# add_custom_target(py2_packages 
#     COMMAND python2 -m pip install -r ${pkgs})

add_custom_target(py3_packages 
    COMMAND python3 -m pip install -r ${pkgs})
add_custom_target(install_packages ALL
    DEPENDS  py3_packages )

add_subdirectory(src)
add_subdirectory(python)
add_subdirectory(tests)



# src

set(python_bindings_dir "${CMAKE_BINARY_DIR}/python-bindings")
add_custom_target(copy_sources
    COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/setup.py ${CMAKE_CURRENT_BINARY_DIR}/setup.py 
    COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/randoms.cc ${CMAKE_CURRENT_BINARY_DIR}/randoms.cc
    COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/randoms.hh ${CMAKE_CURRENT_BINARY_DIR}/randoms.hh
    DEPENDS setup.py)

# add_custom_target(runpython_from_build
# COMMAND python3 ${CMAKE_CURRENT_BINARY_DIR}/copy_of_setup.py
# DEPENDS copy_build
# )

add_custom_target(run_src ALL
    DEPENDS  copy_sources)

add_library(randoms SHARED randoms.cc randoms.hh)
target_include_directories(randoms PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

find_package(PythonLibs REQUIRED)
include_directories(${PYTHON_INCLUDE_DIRS})
target_link_libraries(randoms ${PYTHON_LIBRARIES})

find_package(PythonInterp REQUIRED)
add_custom_command(OUTPUT build_bindings
    COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/setup.py build --build-base=${python_bindings_dir}
    DEPENDS copy_sources) # this needs to depend on the copy process of the python script and the C++ source files otherwise it can fail on make & make install 

add_custom_command(OUTPUT copy_scripts
    COMMAND cp ${CMAKE_SOURCE_DIR}/python/randoms.py ${python_bindings_dir}/lib*
    COMMAND cp ${CMAKE_SOURCE_DIR}/python/histogram.py ${python_bindings_dir}/lib*
    DEPENDS build_bindings
  )# copies the test scripts into lib dir after install
  #once the python scripts which are based on the random module are inside the lib folder, they can be executed without issue
add_custom_command(OUTPUT plot_storage
    COMMAND mkdir -p ${python_bindings_dir}/output
    COMMAND mkdir -p ${python_bindings_dir}/logs
    DEPENDS copy_scripts
  )# creates output directory for python plot storage

install(TARGETS randoms LIBRARY DESTINATION lib)
install(DIRECTORY ${python_bindings_dir} DESTINATION ${CMAKE_INSTALL_PREFIX}/)

# install(FILES randoms.cc randoms.hh setup.py DESTINATION ${CMAKE_INSTALL_PREFIX}/py_dir)
# install(FILES ${python_bindings_dir} DESTINATION ${CMAKE_INSTALL_PREFIX}/ )
# install(FILES ${python_bindings_dir} DESTINATION ${CMAKE_INSTALL_PREFIX}/new-folder )

add_custom_target(run_extension ALL
    DEPENDS copy_sources  build_bindings copy_scripts plot_storage)


# python

configure_file(histogram.py histogram.py COPYONLY)
configure_file(randoms.py randoms.py COPYONLY)

# install(FILES histogram.py DESTINATION ${CMAKE_INSTALL_PREFIX}/${py_dir})
# install(FILES randoms.py DESTINATION ${CMAKE_INSTALL_PREFIX}/${py_dir})

# add_custom_target(py_random_script 
#     COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/randoms.py ${CMAKE_CURRENT_BINARY_DIR}/randoms.py 
#     DEPENDS randoms.py)

# add_custom_target(py_random_script2 
#     COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/randoms.py ${CMAKE_INSTALL_PREFIX}/python-bindings/randoms.py 
#     DEPENDS py_random_script)

# add_custom_target(writeintofile 
#     COMMAND echo NAME >> ${CMAKE_CURRENT_BINARY_DIR}/copy_of_hist.py
#     DEPENDS py_script)

# add_custom_command(TARGET py_script POST_BUILD
#   COMMAND cp ${CMAKE_CURRENT_BINARY_DIR}/copy_of_hist.py ${CMAKE_INSTALL_PREFIX}/python/copy_of_hist.py
#   DEPENDS py_script
# )

# add_custom_command(
#   OUTPUT run_script
#   COMMAND python3 ${CMAKE_CURRENT_SOURCE_DIR}/histogram.py
# )

# add_custom_target(run_python ALL
#     DEPENDS  py_random_script )