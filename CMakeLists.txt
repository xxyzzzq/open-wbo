cmake_minimum_required(VERSION 3.1)
project(open_wbo)

#set(CMAKE_CXX_STANDARD 11)
#set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY
  ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY
  ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY
  ${CMAKE_BINARY_DIR}/bin)

if (NOT OPEN_WBO_SOLVER)
  message(STATUS "OPEN_WBO_SOLVER not specified, defaulting to glucose3.0")
  set(OPEN_WBO_SOLVER "glucose3.0")
endif(NOT OPEN_WBO_SOLVER)

if (${OPEN_WBO_SOLVER} STREQUAL "minisat2.0")
  set(REL_SOLVER_DIR "minisat2.0")
  set(VERSION "core")
  set(SOLVERNAME "Minisat2.0")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "minisat2.2")
  set(REL_SOLVER_DIR "minisat2.2")
  set(VERSION "core")
  set(SOLVERNAME "Minisat2.2")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "glucose3.0")
  set(REL_SOLVER_DIR "glucose3.0")
  set(VERSION "core")
  set(SOLVERNAME "Glucose3.0")
  set(NSPACE "Glucose")
elseif (${OPEN_WBO_SOLVER} STREQUAL "glucose2.3")
  set(REL_SOLVER_DIR "glucose2.3")
  set(VERSION "core")
  set(SOLVERNAME "Glucose2.3")
  set(NSPACE "Glucose")
elseif (${OPEN_WBO_SOLVER} STREQUAL "zenn")
  set(REL_SOLVER_DIR "zenn")
  set(VERSION "core")
  set(SOLVERNAME "ZENN")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "sinn")
  set(REL_SOLVER_DIR "sinn")
  set(VERSION "core")
  set(SOLVERNAME "SINN")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "glueminisat")
  set(REL_SOLVER_DIR "glueminisat")
  set(VERSION "core")
  set(SOLVERNAME "Glueminisat")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "gluh")
  set(REL_SOLVER_DIR "gluH")
  set(VERSION "core")
  set(SOLVERNAME "GluH")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "glue_bit")
  set(REL_SOLVER_DIR "glue_bit")
  set(VERSION "core")
  set(SOLVERNAME "glue_bit")
  set(NSPACE "Minisat")
elseif (${OPEN_WBO_SOLVER} STREQUAL "glucored")
  set(REL_SOLVER_DIR "glucored")
  set(VERSION "simp")
  set(SOLVERNAME "GlucoRed")
  set(NSPACE "Minisat")
  add_definitions(-DGLUCORED)
else (${OPEN_WBO_SOLVER} STREQUAL "minisat2.0")
  message(FATAL_ERROR "Unrecognized solver name OPEN_WBO_SOLVER=\"${OPEN_WBO_SOLVER}\".\n \
Must be one of:\n \
* minisat2.0\n \
* minisat2.2\n \
* glucose3.0\n \
* glucose2.3\n \
* zenn\n \
* sinn\n \
* gluminisat\n \
* gluh\n \
* glue_bit\n \
* glucored")
endif (${OPEN_WBO_SOLVER} STREQUAL "minisat2.0")

set(SOLVER_DIR ${CMAKE_CURRENT_SOURCE_DIR}/solvers/${REL_SOLVER_DIR})

add_definitions(-DVERSION=${VERSION} -DSOLVERNAME=${SOLVERNAME} -DNSPACE=${NSPACE})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-logical-op-parentheses -Wno-dangling-else")

file(
  GLOB
  WBO_SOURCE_FILES
  *.cc algorithms/*.cc encodings/*.cc
  )

file(
  GLOB_RECURSE
  SOLVER_SOURCE_FILES
  ${SOLVER_DIR}/*.cc
  )

file(
  GLOB_RECURSE
  SOLVER_MAIN_FILES
  ${SOLVER_DIR}/*Main.cc
  )

if (SOLVER_MAIN_FILES)
list(
  REMOVE_ITEM
  SOLVER_SOURCE_FILES ${SOLVER_MAIN_FILES}
  )
endif (SOLVER_MAIN_FILES)

include_directories(${SOLVER_DIR}
  )

add_executable(
  open_wbo ${WBO_SOURCE_FILES}
  )

add_library(
  wbo_internal ${SOLVER_SOURCE_FILES}
  )

target_link_libraries(open_wbo wbo_internal z)
