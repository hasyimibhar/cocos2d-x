CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

PROJECT (cocos2d)

SET (CMAKE_INSTALL_PREFIX "${cocos2d_SOURCE_DIR}/dist" CACHE PATH "install prefix" FORCE)

SET (CMAKE_MODULE_PATH
	"${CMAKE_SOURCE_DIR}/cmake/modules/"
	"${CMAKE_BINARY_DIR}/cmake/modules/"
	${CMAKE_MODULE_PATH})

# Determine the architecture.
SET (bitness 32)
IF (CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET (bitness 64)
ENDIF()

MESSAGE (STATUS "Bitness is ${bitness}")

SET (cocos2dx_SOURCE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/cocos2dx)

ADD_SUBDIRECTORY (cocos2dx)
ADD_SUBDIRECTORY (extensions)
ADD_SUBDIRECTORY (CocosDenshion)
