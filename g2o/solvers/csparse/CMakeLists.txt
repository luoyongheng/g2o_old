IF(NOT G2O_USE_CSPARSE)
  RETURN()
ENDIF()

INCLUDE_DIRECTORIES(${CSPARSE_INCLUDE_DIR})

ADD_LIBRARY(csparse_extension ${G2O_LGPL_LIB_TYPE}
  csparse_helper.cpp csparse_helper.h
  g2o_csparse_extension_api.h
)
SET_TARGET_PROPERTIES(csparse_extension PROPERTIES OUTPUT_NAME ${LIB_PREFIX}csparse_extension)
if (APPLE)
  set_target_properties(csparse_extension PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib")
endif()

IF(BUILD_CSPARSE)
  TARGET_LINK_LIBRARIES(csparse_extension PUBLIC csparse ${G2O_EIGEN3_EIGEN_TARGET})
ELSE()
  TARGET_LINK_LIBRARIES(csparse_extension PUBLIC ${CSPARSE_LIBRARY} ${G2O_EIGEN3_EIGEN_TARGET})
ENDIF()

ADD_LIBRARY(solver_csparse ${G2O_LIB_TYPE}
  solver_csparse.cpp
  linear_solver_csparse.h
  g2o_csparse_api.h
)
SET_TARGET_PROPERTIES(solver_csparse PROPERTIES OUTPUT_NAME ${LIB_PREFIX}solver_csparse)
if (APPLE)
  set_target_properties(solver_csparse PROPERTIES INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib")
endif()

TARGET_LINK_LIBRARIES(solver_csparse
  PUBLIC csparse_extension
  PUBLIC core)


INSTALL(TARGETS solver_csparse csparse_extension
  EXPORT ${G2O_TARGETS_EXPORT_NAME}
  RUNTIME DESTINATION ${RUNTIME_DESTINATION}
  LIBRARY DESTINATION ${LIBRARY_DESTINATION}
  ARCHIVE DESTINATION ${ARCHIVE_DESTINATION}
  INCLUDES DESTINATION ${INCLUDES_DESTINATION}
)
FILE(GLOB headers "${CMAKE_CURRENT_SOURCE_DIR}/*.h" "${CMAKE_CURRENT_SOURCE_DIR}/*.hpp")
INSTALL(FILES ${headers} DESTINATION ${INCLUDES_INSTALL_DIR}/solvers/csparse)
