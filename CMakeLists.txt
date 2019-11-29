CMAKE_MINIMUM_REQUIRED(VERSION 3.11)
# =============================================================================
PROJECT(bzip2 VERSION 1.0.8 LANGUAGES C)
# =============================================================================
IF(MSVC)
  ADD_DEFINITIONS(-D_FILE_OFFSET_BITS=64)
  ADD_DEFINITIONS(-D_CRT_SECURE_NO_DEPRECATE)
  ADD_DEFINITIONS(-D_CRT_NONSTDC_NO_DEPRECATE)
ENDIF(MSVC)
# -----------------------------------------------------------------------------
SET(CMAKE_DEBUG_POSTFIX "d")
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
ADD_LIBRARY(bz2 SHARED
  ${LIBBZ2__HDR}
  ${LIBBZ2__SRC}
  ${LIBBZ2__DEF}
)
# -----------------------------------------------------------------------------
ADD_LIBRARY(bz2s STATIC
  ${LIBBZ2__HDR}
  ${LIBBZ2__SRC}
)
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
