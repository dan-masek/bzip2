CMAKE_MINIMUM_REQUIRED(VERSION 3.11)
# =============================================================================
PROJECT(bzip2 VERSION 1.0.8 LANGUAGES C)
# =============================================================================
set(INSTALL_BIN_DIR "bin" CACHE PATH "Installation directory for executables")
set(INSTALL_LIB_DIR "lib" CACHE PATH "Installation directory for libraries")
set(INSTALL_INC_DIR "include" CACHE PATH "Installation directory for headers")
set(INSTALL_TOOL_DIR "tool" CACHE PATH "Installation directory for tools")
# =============================================================================
IF(MSVC)
  ADD_DEFINITIONS(-D_FILE_OFFSET_BITS=64)
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_DEPRECATE)
  ADD_DEFINITIONS(-D_CRT_NONSTDC_NO_DEPRECATE)
ENDIF(MSVC)
# -----------------------------------------------------------------------------
SET(CMAKE_DEBUG_POSTFIX "-debug")
# =============================================================================
LIST(APPEND LIBBZ2__HDR
  bzlib.h
  bzlib_private.h
)
LIST(APPEND LIBBZ2__SRC
  blocksort.c
  huffman.c
  crctable.c
  randtable.c 
  compress.c
  decompress.c
  bzlib.c
)
LIST(APPEND LIBBZ2__DEF
)
# -----------------------------------------------------------------------------
IF(MSVC)
  SET(VERSION_PRODUCT_NAME_STR "bzip2 - a freely available, patent free, high-quality data compressor.")
  SET(VERSION_COPYRIGHT_STR "Copyright (c) 1996-2010 Julian R Seward")
  SET(VERSION_STR "${PROJECT_VERSION}")
  SET(VERSION_NUM "${PROJECT_VERSION_MAJOR},${PROJECT_VERSION_MINOR},${PROJECT_VERSION_PATCH},0")
  SET(VERSION_ORIGINALFILENAME_STR "bz2.dll")
  SET(VERSION_INTERNALNAME_STR "bz2")
  SET(VERSION_FILEDESCRIPTION_STR "bz2 Dynamic Link Library")
  CONFIGURE_FILE(
    ${CMAKE_SOURCE_DIR}/dllversion.rc.in
    ${CMAKE_CURRENT_BINARY_DIR}/dllversion.rc
    @ONLY
  )
  LIST(APPEND LIBBZ2__DEF
    ${CMAKE_SOURCE_DIR}/libbz2.def
    ${CMAKE_CURRENT_BINARY_DIR}/dllversion.rc
  )
ENDIF(MSVC)
# -----------------------------------------------------------------------------
IF(MSVC14)
  SET(VS_COMPILER_TAG vs14)
ELSE(MSVC14)
  MESSAGE(FATAL_ERROR "Unsupported compiler.")
ENDIF()
IF(CMAKE_CL_64)
  SET(VS_ARCH_TAG x64)
ELSE(CMAKE_CL_64)
  SET(VS_ARCH_TAG x86)
ENDIF()
STRING(REPLACE "." "_" VS_VERSION_TAG ${PROJECT_VERSION})
SET(VS_VTAG ${VS_COMPILER_TAG}-${VS_ARCH_TAG}-${VS_VERSION_TAG})
# -----------------------------------------------------------------------------
ADD_LIBRARY(bz2 SHARED
  ${LIBBZ2__HDR}
  ${LIBBZ2__SRC}
  ${LIBBZ2__DEF}
)
SET_TARGET_PROPERTIES(bz2 PROPERTIES OUTPUT_NAME "bz2-${VS_VTAG}")
# -----------------------------------------------------------------------------
ADD_LIBRARY(bz2static STATIC
  ${LIBBZ2__HDR}
  ${LIBBZ2__SRC}
)
SET_TARGET_PROPERTIES(bz2static PROPERTIES OUTPUT_NAME "bz2static-${VS_VTAG}")
# =============================================================================
LIST(APPEND BZIP2__SRC
  bzip2.c
)
# -----------------------------------------------------------------------------
ADD_EXECUTABLE(bzip2
  ${BZIP2__SRC}
)
TARGET_LINK_LIBRARIES(bzip2 bz2)
# =============================================================================
LIST(APPEND BZIP2_RECOVER__SRC
  bzip2recover.c
)
# -----------------------------------------------------------------------------
ADD_EXECUTABLE(bzip2recover
  ${BZIP2_RECOVER__SRC}
)
TARGET_LINK_LIBRARIES(bzip2recover bz2)
# =============================================================================
install(TARGETS bz2
    RUNTIME DESTINATION "${INSTALL_BIN_DIR}"
    ARCHIVE DESTINATION "${INSTALL_LIB_DIR}"
    LIBRARY DESTINATION "${INSTALL_LIB_DIR}" )
install(TARGETS bz2static
    RUNTIME DESTINATION "${INSTALL_BIN_DIR}/static"
    ARCHIVE DESTINATION "${INSTALL_LIB_DIR}/static"
    LIBRARY DESTINATION "${INSTALL_LIB_DIR}/static" )
install(FILES $<TARGET_PDB_FILE:bz2> DESTINATION "${INSTALL_BIN_DIR}" OPTIONAL)
install(FILES bzlib.h DESTINATION "${INSTALL_INC_DIR}")
install(TARGETS bzip2 RUNTIME DESTINATION "${INSTALL_TOOL_DIR}" CONFIGURATIONS RelWithDebInfo)
install(FILES $<TARGET_PDB_FILE:bzip2> DESTINATION "${INSTALL_TOOL_DIR}" CONFIGURATIONS RelWithDebInfo OPTIONAL)
install(TARGETS bzip2recover RUNTIME DESTINATION "${INSTALL_TOOL_DIR}" CONFIGURATIONS RelWithDebInfo)
install(FILES $<TARGET_PDB_FILE:bzip2recover> DESTINATION "${INSTALL_TOOL_DIR}" CONFIGURATIONS RelWithDebInfo OPTIONAL)
# =============================================================================
