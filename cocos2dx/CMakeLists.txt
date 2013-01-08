CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

# Set up the environment.
SET (SRC ${CMAKE_CURRENT_SOURCE_DIR})
SET (PROJ "cocos2dx")

PROJECT (${PROJ})

# Clear variables for our current project.
SET (${PROJ}_SOURCES)
SET (${PROJ}_INCLUDE_DIRS)
SET (${PROJ}_LIBRARIES)

# Find required libraries.
#FIND_PACKAGE (OpenGLES REQUIRED)
FIND_PACKAGE (ZLIB REQUIRED)
FIND_PACKAGE (LibXml2 REQUIRED)
#FIND_PACKAGE (Threads REQUIRED)

#MESSAGE (STATUS ${OpenGLES_LIBRARIES})

#IF (NOT EGL_FOUND)
#	MESSAGE(FATAL_ERROR "Noooooo")
#ENDIF ()

IF (ANDROID)
	#TODO: Move these into /platform/android/CMakeLists.txt
	ADD_DEFINITIONS (-DANDROID)

	# Platform-specific includes.
	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/platform/android)

	# Platform-specific sources.
	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/platform/android/CCAccelerometer.cpp
		${SRC}/platform/android/CCApplication.cpp
		${SRC}/platform/android/CCCommon.cpp
		${SRC}/platform/android/CCEGLView.cpp
		${SRC}/platform/android/CCFileUtils.cpp
		${SRC}/platform/android/CCImage.cpp

		${SRC}/platform/android/jni/IMEJni.cpp
		${SRC}/platform/android/jni/Java_org_cocos2dx_lib_Cocos2dxAccelerometer.cpp
		${SRC}/platform/android/jni/Java_org_cocos2dx_lib_Cocos2dxBitmap.cpp
		${SRC}/platform/android/jni/Java_org_cocos2dx_lib_Cocos2dxHelper.cpp
		${SRC}/platform/android/jni/Java_org_cocos2dx_lib_Cocos2dxRenderer.cpp
		${SRC}/platform/android/jni/JniHelper.cpp
		${SRC}/platform/android/jni/TouchesJni.cpp)

	SET (${PROJ}_LIBRARIES
		${${PROJ}_LIBRARIES}
		log)

ELSEIF (IOS)

	ADD_DEFINITIONS (-DCC_TARGET_OS_IPHONE)
	ADD_DEFINITIONS (-DUSE_FILE32API)

	FIND_LIBRARY (UIKIT_LIBRARY UIKit)

	# Platform-specific includes.
	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/platform/ios
		${SRC}/platform/third_party/ios)

	SET (${PROJ}_LIBRARIES
		${${PROJ}_LIBRARIES}
		${SRC}/platform/third_party/ios/libraries/libcurl.a
		${UIKIT_LIBRARY})

	# Platform-specific sources.
	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/platform/ios/AccelerometerDelegateWrapper.mm
		${SRC}/platform/ios/CCAccelerometer.mm
		${SRC}/platform/ios/CCApplication.mm
		${SRC}/platform/ios/CCCommon.mm
		${SRC}/platform/ios/CCDirectorCaller.mm
		${SRC}/platform/ios/CCEGLView.mm
		${SRC}/platform/ios/CCFileUtils.mm
		${SRC}/platform/ios/CCImage.mm
		${SRC}/platform/ios/CCThread.mm
		${SRC}/platform/ios/EAGLView.mm
		${SRC}/platform/ios/CCES2Renderer.m
		${SRC}/platform/ios/CCLock.cpp
		${SRC}/platform/ios/Simulation/AccelerometerSimulation.m)

ELSEIF (APPLE)

	ADD_DEFINITIONS (-DCC_TARGET_OS_MAC)

	# Platform-specific includes.
	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/platform/mac)

	# Platform-specific sources.
	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/platform/mac/CCApplication.mm
		${SRC}/platform/mac/CCCommon.mm
		${SRC}/platform/mac/CCDirectorCaller.mm
		${SRC}/platform/mac/CCEGLView.mm
		${SRC}/platform/mac/CCEventDispatcher.mm
		${SRC}/platform/mac/CCFileUtils.mm
		${SRC}/platform/mac/CCImage.mm
		${SRC}/platform/mac/CCThread.mm
		${SRC}/platform/mac/EAGLView.mm
		${SRC}/platform/mac/CCWindow.m)

ELSEIF (UNIX)
	#TODO: Move these into /platform/linux/CMakeLists.txt
	ADD_DEFINITIONS (-DLINUX
		-D__CC_PLATFORM_FILEUTILS_CPP__
		-D__CC_PLATFORM_IMAGE_CPP__)

	FIND_PACKAGE (Freetype REQUIRED)
	FIND_PACKAGE (GLFW REQUIRED)
	FIND_PACKAGE (CURL REQUIRED)
	FIND_PACKAGE (PNG REQUIRED)
	FIND_PACKAGE (JPEG REQUIRED)
	FIND_PACKAGE (TIFF REQUIRED)
	FIND_PACKAGE (OpenGL REQUIRED)
	FIND_PACKAGE (GLEW REQUIRED)

	# Platform-specific includes.
	SET (${PROJ}_INCLUDE_DIRS
		${${PROJ}_INCLUDE_DIRS}
		${SRC}/platform/linux
		${FREETYPE_INCLUDE_DIRS})

	# Platform-specific libraries.
	SET (${PROJ}_LIBRARIES
		${${PROJ}_LIBRARIES}
		${OPENGL_LIBRARIES}
		${FREETYPE_LIBRARIES}
		${GLFW_LIBRARIES}
		${CURL_LIBRARIES}
		${PNG_LIBRARIES}
		${JPEG_LIBRARIES}
		${TIFF_LIBRARIES}
		${GLEW_LIBRARY})

	# Platform-specific sources.
	SET (${PROJ}_SOURCES
		${${PROJ}_SOURCES}
		${SRC}/platform/linux/CCApplication.cpp
		${SRC}/platform/linux/CCCommon.cpp
		${SRC}/platform/linux/CCEGLView.cpp
		${SRC}/platform/linux/CCFileUtils.cpp
		${SRC}/platform/linux/CCImage.cpp
		${SRC}/platform/linux/CCStdC.cpp)

ELSEIF (WIN32)

	ADD_DEFINITIONS (-DWIN32)

ENDIF ()

SET (${PROJ}_INCLUDE_DIRS
	${${PROJ}_INCLUDE_DIRS}
	${SRC}
	${SRC}/actions
	${SRC}/base_nodes
	${SRC}/cocoa
	${SRC}/draw_nodes
	${SRC}/effects
	${SRC}/include
	${SRC}/kazmath/include
	${SRC}/keypad_dispatcher
	${SRC}/label_nodes
	${SRC}/layers_scenes_transitions_nodes
	${SRC}/menu_nodes
	${SRC}/misc_nodes
	${SRC}/particle_nodes
	${SRC}/platform
	${SRC}/script_support
	${SRC}/shaders
	${SRC}/sprite_nodes
	${SRC}/support
	${SRC}/text_input_node
	${SRC}/textures
	${SRC}/tilemap_parallax_nodes
	${SRC}/touch_dispatcher)

SET (${PROJ}_SOURCES
	${${PROJ}_SOURCES}

	# ./
	${SRC}/CCCamera.cpp
	${SRC}/CCConfiguration.cpp
	${SRC}/CCDirector.cpp
	${SRC}/CCScheduler.cpp
	${SRC}/cocos2d.cpp

	# ./actions/
	${SRC}/actions/CCAction.cpp
	${SRC}/actions/CCActionCamera.cpp
	${SRC}/actions/CCActionCatmullRom.cpp
	${SRC}/actions/CCActionEase.cpp
	${SRC}/actions/CCActionGrid.cpp
	${SRC}/actions/CCActionGrid3D.cpp
	${SRC}/actions/CCActionInstant.cpp
	${SRC}/actions/CCActionInterval.cpp
	${SRC}/actions/CCActionManager.cpp
	${SRC}/actions/CCActionPageTurn3D.cpp
	${SRC}/actions/CCActionProgressTimer.cpp
	${SRC}/actions/CCActionTiledGrid.cpp
	${SRC}/actions/CCActionTween.cpp

	# ./base_nodes/
	${SRC}/base_nodes/CCAtlasNode.cpp
	${SRC}/base_nodes/CCNode.cpp

	# ./cocoa/
	${SRC}/cocoa/CCAffineTransform.cpp
	${SRC}/cocoa/CCArray.cpp
	${SRC}/cocoa/CCAutoreleasePool.cpp
	${SRC}/cocoa/CCDictionary.cpp
	${SRC}/cocoa/CCGeometry.cpp
	${SRC}/cocoa/CCNS.cpp
	${SRC}/cocoa/CCObject.cpp
	${SRC}/cocoa/CCSet.cpp
	${SRC}/cocoa/CCString.cpp
	${SRC}/cocoa/CCZone.cpp

	# ./draw_nodes/
	${SRC}/draw_nodes/CCDrawingPrimitives.cpp
	${SRC}/draw_nodes/CCDrawNode.cpp

	# ./effects/
	${SRC}/effects/CCGrabber.cpp
	${SRC}/effects/CCGrid.cpp

	# ./kazmath/
	${SRC}/kazmath/src/aabb.c
	${SRC}/kazmath/src/mat3.c
	${SRC}/kazmath/src/mat4.c
	${SRC}/kazmath/src/neon_matrix_impl.c
	${SRC}/kazmath/src/plane.c
	${SRC}/kazmath/src/quaternion.c
	${SRC}/kazmath/src/ray2.c
	${SRC}/kazmath/src/utility.c
	${SRC}/kazmath/src/vec2.c
	${SRC}/kazmath/src/vec3.c
	${SRC}/kazmath/src/vec4.c
	${SRC}/kazmath/src/GL/mat4stack.c
	${SRC}/kazmath/src/GL/matrix.c

	# ./keypad_dispatcher/
	${SRC}/keypad_dispatcher/CCKeypadDelegate.cpp
	${SRC}/keypad_dispatcher/CCKeypadDispatcher.cpp

	# ./label_nodes/
	${SRC}/label_nodes/CCLabelAtlas.cpp
	${SRC}/label_nodes/CCLabelBMFont.cpp
	${SRC}/label_nodes/CCLabelTTF.cpp

	# ./layers_scenes_transitions_nodes/
	${SRC}/layers_scenes_transitions_nodes/CCLayer.cpp
	${SRC}/layers_scenes_transitions_nodes/CCScene.cpp
	${SRC}/layers_scenes_transitions_nodes/CCTransition.cpp
	${SRC}/layers_scenes_transitions_nodes/CCTransitionPageTurn.cpp
	${SRC}/layers_scenes_transitions_nodes/CCTransitionProgress.cpp

	# ./menu_nodes/
	${SRC}/menu_nodes/CCMenu.cpp
	${SRC}/menu_nodes/CCMenuItem.cpp

	# ./misc_nodes/
	${SRC}/misc_nodes/CCClippingNode.cpp
	${SRC}/misc_nodes/CCMotionStreak.cpp
	${SRC}/misc_nodes/CCProgressTimer.cpp
	${SRC}/misc_nodes/CCRenderTexture.cpp

	# ./particle_nodes/
	${SRC}/particle_nodes/CCParticleBatchNode.cpp
	${SRC}/particle_nodes/CCParticleExamples.cpp
	${SRC}/particle_nodes/CCParticleSystem.cpp
	${SRC}/particle_nodes/CCParticleSystemQuad.cpp

	# ./platform/
	${SRC}/platform/CCEGLViewProtocol.cpp
	${SRC}/platform/CCSAXParser.cpp
	${SRC}/platform/CCThread.cpp
	${SRC}/platform/platform.cpp

	# ./script_support/
	${SRC}/script_support/CCScriptSupport.cpp

	# ./shaders/
	${SRC}/shaders/CCGLProgram.cpp
	${SRC}/shaders/ccGLStateCache.cpp
	${SRC}/shaders/CCShaderCache.cpp
	${SRC}/shaders/ccShaders.cpp

	# ./sprite_nodes/
	${SRC}/sprite_nodes/CCAnimation.cpp
	${SRC}/sprite_nodes/CCAnimationCache.cpp
	${SRC}/sprite_nodes/CCSprite.cpp
	${SRC}/sprite_nodes/CCSpriteBatchNode.cpp
	${SRC}/sprite_nodes/CCSpriteFrame.cpp
	${SRC}/sprite_nodes/CCSpriteFrameCache.cpp

	# ./support/
	${SRC}/support/base64.cpp
	${SRC}/support/CCNotificationCenter.cpp
	${SRC}/support/CCPointExtension.cpp
	${SRC}/support/CCProfiling.cpp
	${SRC}/support/CCUserDefault.cpp
	${SRC}/support/ccUtils.cpp
	${SRC}/support/CCVertex.cpp
	${SRC}/support/TransformUtils.cpp
	${SRC}/support/data_support/ccCArray.cpp
	${SRC}/support/image_support/TGAlib.cpp
	${SRC}/support/zip_support/ioapi.cpp
	${SRC}/support/zip_support/unzip.cpp
	${SRC}/support/zip_support/ZipUtils.cpp

	# ./text_input_node/
	${SRC}/text_input_node/CCIMEDispatcher.cpp
	${SRC}/text_input_node/CCTextFieldTTF.cpp

	# ./textures/
	${SRC}/textures/CCTexture2D.cpp
	${SRC}/textures/CCTextureAtlas.cpp
	${SRC}/textures/CCTextureCache.cpp
	${SRC}/textures/CCTexturePVR.cpp

	# ./tilemap_parallax_nodes/
	${SRC}/tilemap_parallax_nodes/CCParallaxNode.cpp
	${SRC}/tilemap_parallax_nodes/CCTileMapAtlas.cpp
	${SRC}/tilemap_parallax_nodes/CCTMXLayer.cpp
	${SRC}/tilemap_parallax_nodes/CCTMXObjectGroup.cpp
	${SRC}/tilemap_parallax_nodes/CCTMXTiledMap.cpp
	${SRC}/tilemap_parallax_nodes/CCTMXXMLParser.cpp

	# ./touch_dispatcher/
	${SRC}/touch_dispatcher/CCTouch.cpp
	${SRC}/touch_dispatcher/CCTouchDispatcher.cpp
	${SRC}/touch_dispatcher/CCTouchHandler.cpp)

SET (${PROJ}_INCLUDE_DIRS
	${${PROJ}_INCLUDE_DIRS}
	${ZLIB_INCLUDES}
	${LIBXML2_INCLUDE_DIR})

SET (${PROJ}_LIBRARIES
	${${PROJ}_LIBRARIES}
	"-lpthread"
	#GLESv2 #FIXME
	#${EGL_LIBRARIES}
	${ZLIB_LIBRARIES}
	${LIBXML2_LIBRARIES}
	${COCOS2DX_PLATFORM_LIBRARIES})

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

GET_CMAKE_PROPERTY (_variableNames VARIABLES)
FOREACH (_variableName ${_variableNames})
    MESSAGE (STATUS "${_variableName}=${${_variableName}}")
ENDFOREACH ()

# Create a module configuration file for this project.
CONFIGURE_FILE (${CMAKE_SOURCE_DIR}/CMake/Modules/FindModule.cmake.in
	${CMAKE_BINARY_DIR}/CMake/Modules/Find${PROJ}.cmake)

# Make this variable visible to the parent.
SET (${PROJ}_INCLUDE_DIRS ${${PROJ}_INCLUDE_DIRS} PARENT_SCOPE)