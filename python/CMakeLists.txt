cmake_minimum_required (VERSION 2.6)
FIND_PACKAGE(SWIG REQUIRED)
INCLUDE(${SWIG_USE_FILE})

FIND_PACKAGE(PythonLibs)
INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_PATH})

FIND_PROGRAM(PYTHON_EXECUTABLE NAMES python python3.4 python3.3 python3.2 python3.1 python3.0 python3 python2.7)
message("python: " ${PYTHON_EXECUTABLE})

EXEC_PROGRAM ("${PYTHON_EXECUTABLE}"
  ARGS "-c 'import numpy; print(numpy.get_include())'"
  OUTPUT_VARIABLE NUMPY_INCLUDE_DIR
  RETURN_VALUE NUMPY_NOT_FOUND)
message("numpy : " ${NUMPY_INCLUDE_DIR})
INCLUDE_DIRECTORIES(${NUMPY_INCLUDE_DIR})

INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR})
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_SOURCE_DIR}/../lib/inc)

SET(CMAKE_SWIG_FLAGS "")

SET_SOURCE_FILES_PROPERTIES(rwt.i PROPERTIES CPLUSPLUS ON)
#SET_SOURCE_FILES_PROPERTIES(rwt.i PROPERTIES SWIG_FLAGS "-includeall")
SWIG_ADD_MODULE(rwt python rwt.i ../lib/src/dwt.cc ../lib/src/idwt.cc ../lib/src/rdwt.c ../lib/src/irdwt.c ../lib/src/platform.c ../lib/src/init.c)
SWIG_LINK_LIBRARIES(rwt ${PYTHON_LIBRARIES})

execute_process(COMMAND python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())" OUTPUT_VARIABLE PYTHON_SITE_PACKAGES OUTPUT_STRIP_TRAILING_WHITESPACE)
install(TARGETS _rwt DESTINATION ${PYTHON_SITE_PACKAGES})
install(FILES rwt.py DESTINATION ${PYTHON_SITE_PACKAGES})
