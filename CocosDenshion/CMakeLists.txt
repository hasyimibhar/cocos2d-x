CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

# Set up the environment.
SET (SRC ${CMAKE_CURRENT_SOURCE_DIR})
SET (PROJ "CocosDenshion")

PROJECT (${PROJ})

# Clear variables for our current project.
SET (${PROJ}_SOURCES)
SET (${PROJ}_INCLUDE_DIRS)
SET (${PROJ}_LINK_DIRS)
SET (${PROJ}_LIBRARIES)

FIND_PACKAGE (cocos2dx REQUIRED)

SET (${PROJ}_INCLUDE_DIRS
	${${PROJ}_INCLUDE_DIRS}
	${SRC}
	${SRC}/include
	${cocos2dx_INCLUDE_DIRS})
LINK_DIRECTORIES (${PROJECT_BINARY_DIR}/cocos2dx)

# Determine the architecture.
SET (bitness 32)
IF (CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET (bitness 64)
ENDIF ()

# Determine target platform.
# TODO: Add IOS and MacOSX
IF (ANDROID)

	ADD_DEFINITIONS (-DANDROID)

	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/android)

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/android/SimpleAudioEngine.cpp
		${SRC}/android/jni/SimpleAudioEngineJni.cpp
		${SRC}/android/opensl/OpenSLEngine.cpp
		${SRC}/android/opensl/SimpleAudioEngineOpenSL.cpp)

ELSEIF (IOS)

	ADD_DEFINITIONS (-DCC_TARGET_OS_IPHONE)

	SET (${PROJ}_LIBRARIES
		${${PROJ}_LIBRARIES})

	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/ios)

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/ios/CDAudioManager.m
		${SRC}/ios/CDOpenALSupport.m
		${SRC}/ios/CocosDenshion.m
		${SRC}/ios/SimpleAudioEngine_objc.m
		${SRC}/ios/SimpleAudioEngine.mm)

# MacOSX
ELSEIF (APPLE)

	ADD_DEFINITIONS (-DCC_TARGET_OS_MAC)

	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/mac)

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/mac/CDAudioManager.m
		${SRC}/mac/CDOpenALSupport.m
		${SRC}/mac/CDXMacOSXSupport.mm
		${SRC}/mac/CocosDenshion.m
		${SRC}/mac/SimpleAudioEngine_objc.m
		${SRC}/mac/SimpleAudioEngine.mm)

ELSEIF (UNIX)

	ADD_DEFINITIONS (-DLINUX)
	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/linux)

	IF (bitness EQUAL 64)

		# Include 64-bit FMOD
		SET (${PROJ}_INCLUDE_DIRS
			${${PROJ}_INCLUDE_DIRS}
			${SRC}/third_party/fmod/lib64/api/inc)
		LINK_DIRECTORIES (${SRC}/third_party/fmod/lib64/api/lib)

		SET (${PROJ}_LIBRARIES
			${${PROJ}_LIBRARIES}
			fmodex64)

	ELSE ()

		# Include 32-bit FMOD
		SET (${PROJ}_INCLUDE_DIRS
			${${PROJ}_INCLUDE_DIRS}
			${SRC}/third_party/fmod/lib/api/inc)
		LINK_DIRECTORIES (${SRC}/third_party/fmod/lib/api/lib)

		SET (${PROJ}_LIBRARIES
			${${PROJ}_LIBRARIES}
			fmodex)

	ENDIF ()

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/linux/FmodAudioPlayer.cpp
		${SRC}/linux/SimpleAudioEngine.cpp
	)

ELSEIF (WIN32)

	ADD_DEFINITIONS (-DWIN32)

	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/win32)

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/win32/MciPlayer.cpp
		${SRC}/win32/SimpleAudioEngine.cpp)

ENDIF ()

# Create the actual target.
INCLUDE_DIRECTORIES (${${PROJ}_INCLUDE_DIRS})
ADD_LIBRARY (${PROJ} ${${PROJ}_SOURCES})
TARGET_LINK_LIBRARIES (${PROJ} ${${PROJ}_LIBRARIES})

MESSAGE (STATUS "PROJ: " ${PROJ})
MESSAGE (STATUS "SRC: " ${SRC})

FOREACH (item ${${PROJ}_SOURCES})
    MESSAGE (STATUS ${PROJ} "_SOURCES: " ${item})
ENDFOREACH (item)

FOREACH (item ${${PROJ}_INCLUDE_DIRS})
    MESSAGE (STATUS ${PROJ} "_INCLUDE_DIRS: " ${item})
ENDFOREACH (item)

FOREACH (item ${${PROJ}_LIBRARIES})
    MESSAGE (STATUS ${PROJ} "_LIBRARIES: " ${item})
ENDFOREACH (item)

# Create a module configuration file for this project.
CONFIGURE_FILE (${CMAKE_SOURCE_DIR}/CMake/Modules/FindModule.cmake.in
	${CMAKE_BINARY_DIR}/CMake/Modules/Find${PROJ}.cmake)

# Make this variable visible to the parent.
SET (${PROJ}_INCLUDE_DIRS ${${PROJ}_INCLUDE_DIRS} PARENT_SCOPE)