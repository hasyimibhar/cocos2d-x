CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

# Set up the environment.
SET (SRC ${CMAKE_CURRENT_SOURCE_DIR})
SET (PROJ "extensions")

PROJECT (${PROJ})

# Clear variables for our current project.
SET (${PROJ}_SOURCES)
SET (${PROJ}_INCLUDE_DIRS)
SET (${PROJ}_LIBRARIES)

FIND_PACKAGE (cocos2dx REQUIRED)

SET (${PROJ}_INCLUDE_DIRS
	${${PROJ}_INCLUDE_DIRS}
	${SRC}
	${cocos2dx_INCLUDE_DIRS})

LINK_DIRECTORIES (${PROJECT_BINARY_DIR}/cocos2dx)

SET (${PROJ}_SOURCES
	${${PROJ}_SOURCES}

	# CCBReader
	${SRC}/CCBReader/CCBAnimationManager.cpp
	${SRC}/CCBReader/CCBFileLoader.cpp
	${SRC}/CCBReader/CCBKeyframe.cpp
	${SRC}/CCBReader/CCBReader.cpp
	${SRC}/CCBReader/CCBSequence.cpp
	${SRC}/CCBReader/CCBSequenceProperty.cpp
	${SRC}/CCBReader/CCBValue.cpp
	${SRC}/CCBReader/CCControlButtonLoader.cpp
	${SRC}/CCBReader/CCControlLoader.cpp
	${SRC}/CCBReader/CCData.cpp
	${SRC}/CCBReader/CCLabelBMFontLoader.cpp
	${SRC}/CCBReader/CCLabelTTFLoader.cpp
	${SRC}/CCBReader/CCLayerColorLoader.cpp
	${SRC}/CCBReader/CCLayerGradientLoader.cpp
	${SRC}/CCBReader/CCLayerLoader.cpp
	${SRC}/CCBReader/CCMenuItemImageLoader.cpp
	${SRC}/CCBReader/CCMenuItemLoader.cpp
	${SRC}/CCBReader/CCNode+CCBRelativePositioning.cpp
	${SRC}/CCBReader/CCNodeLoader.cpp
	${SRC}/CCBReader/CCNodeLoaderLibrary.cpp
	${SRC}/CCBReader/CCParticleSystemQuadLoader.cpp
	${SRC}/CCBReader/CCScale9SpriteLoader.cpp
	${SRC}/CCBReader/CCScrollViewLoader.cpp
	${SRC}/CCBReader/CCSpriteLoader.cpp

	# CCControlExtension
	${SRC}/GUI/CCControlExtension/CCControl.cpp
	${SRC}/GUI/CCControlExtension/CCControlButton.cpp
	${SRC}/GUI/CCControlExtension/CCControlColourPicker.cpp
	${SRC}/GUI/CCControlExtension/CCControlHuePicker.cpp
	${SRC}/GUI/CCControlExtension/CCControlPotentiometer.cpp
	${SRC}/GUI/CCControlExtension/CCControlSaturationBrightnessPicker.cpp
	${SRC}/GUI/CCControlExtension/CCControlSlider.cpp
	${SRC}/GUI/CCControlExtension/CCControlStepper.cpp
	${SRC}/GUI/CCControlExtension/CCControlSwitch.cpp
	${SRC}/GUI/CCControlExtension/CCControlUtils.cpp
	${SRC}/GUI/CCControlExtension/CCInvocation.cpp
	${SRC}/GUI/CCControlExtension/CCScale9Sprite.cpp

	# CCEditBox
	${SRC}/GUI/CCEditBox/CCEditBox.cpp

	# CCScrollView
	${SRC}/GUI/CCScrollView/CCScrollView.cpp
	${SRC}/GUI/CCScrollView/CCSorting.cpp
	${SRC}/GUI/CCScrollView/CCTableView.cpp
	${SRC}/GUI/CCScrollView/CCTableViewCell.cpp

	# network
	${SRC}/network/HttpClient.cpp

	# physics_nodes
	${SRC}/physics_nodes/CCPhysicsDebugNode.cpp
	${SRC}/physics_nodes/CCPhysicsSprite.cpp
	)

########################################################
# Platform specific stuff 
########################################################

# Android
IF (ANDROID)

	ADD_DEFINITIONS (-DANDROID)

	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}

		${SRC}/GUI/CCEditBox/CCEditBoxImplAndroid.cpp
		${SRC}/LocalStorage/LocalStorageAndroid.cpp)

ELSE ()

	SET (${PROJ}_SOURCES 
		${${PROJ}_SOURCES} ${SRC}/LocalStorage/LocalStorage.cpp)

	# iOS
	IF (IOS)

		ADD_DEFINITIONS (-DCC_TARGET_OS_IPHONE)

		SET (${PROJ}_SOURCES 
			${${PROJ}_SOURCES} ${SRC}/GUI/CCEditBox/CCEditBoxImplIOS.mm)

	# MacOSX
	ELSEIF (APPLE)

		ADD_DEFINITIONS (-DCC_TARGET_OS_MAC)

	# Unix
	ELSEIF (UNIX)

		ADD_DEFINITIONS (-DLINUX)

	# Windows
	ELSEIF (WIN32)

		ADD_DEFINITIONS (-DWIN32)

	ENDIF ()

ENDIF ()

########################################################

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
CONFIGURE_FILE (${CMAKE_SOURCE_DIR}/cmake/modules/FindModule.cmake.in
	${CMAKE_BINARY_DIR}/cmake/modules/Find${PROJ}.cmake)

# Make this variable visible to the parent.
SET (${PROJ}_INCLUDE_DIRS ${${PROJ}_INCLUDE_DIRS} PARENT_SCOPE)