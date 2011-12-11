INCLUDE(PolycodeIncludes)
    
INCLUDE_DIRECTORIES(
    ${LUA_INCLUDE_DIR}
    ${Polycode_SOURCE_DIR}/Bindings/Contents/LUA/Include
    Include
    ../../Core/Contents/PolycodeView/Mac\ OS\ X/
)

SET(CMAKE_DEBUG_POSTFIX "_d")

IF(MSVC)
    SET(polycodeplayer_SRCS
        ../Build/MSVC/PolycodePlayer/player_icon.ico
        ../Build/MSVC/PolycodePlayer/main.cpp
        ../Build/MSVC/PolycodePlayer/PolycodePlayerView.cpp
        Source/PolycodePlayer.cpp
        Source/PolycodeWindowsPlayer.cpp
    )
    SET(polycodeplayer_HDRS
        ../Build/MSVC/PolycodePlayer/resource.h
        ../Build/MSVC/PolycodePlayer/PolycodePlayerView.h
        Include/PolycodePlayer.h 
        Include/PolycodeWindowsPlayer.h
    )
    
    INCLUDE_DIRECTORIES(../Build/MSVC/PolycodePlayer Include)
    
 #   IF(POLYCODE_BUILD_SHARED)
 #       ADD_EXECUTABLE(PolycodePlayer_dynamic WIN32 ${polycodeplayer_SRCS} ${polycodeplayer_HDRS})
 #       TARGET_LINK_LIBRARIES(PolycodePlayer Polycore_dynamic PolycodeLua ${LUA_LIBRARY})
 #   ENDIF(POLYCODE_BUILD_SHARED)

 #   IF(POLYCODE_BUILD_STATIC)
    ADD_EXECUTABLE(PolycodePlayer WIN32 ${polycodeplayer_SRCS} ${polycodeplayer_HDRS})
    TARGET_LINK_LIBRARIES(PolycodePlayer 
        Polycore 
        PolycodeLua 
        ${LUA_LIBRARY}
        ${OPENGL_LIBRARIES}
        ${OPENAL_LIBRARY}
        ${PNG_LIBRARIES}
        ${FREETYPE_LIBRARIES}
        ${PHYSFS_LIBRARY}
        ${OGG_LIBRARY}
        ${VORBIS_LIBRARY}
        ${VORBISFILE_LIBRARY}
     )

 #   ENDIF(POLYCODE_BUILD_STATIC)
    
ELSEIF(APPLE)
    SET(polycodeplayer_SRCS 
        Source/PolycodePlayer.cpp
        Source/PolycodeCocoaPlayer.mm
        Platform/Darwin/main.m
        Platform/Darwin/MyDocument.mm
        Platform/Darwin/PPlayerDocumentController.mm
	../../Core/Contents/PolycodeView/Mac\ OS\ X/PolycodeView.mm
    )
    SET(polycodeplayer_HDRS 
        Include/PolycodePlayer.h
        Include/PolycodeCocoaPlayer.h
        Platform/Darwin/MyDocument.h
        Platform/Darwin/PPlayerDocumentController.h
	../../Core/Contents/PolycodeView/Mac\ OS\ X/PolycodeView.h
    )
  
    SET(polycodeplayerstandalone_SRCS 
        Source/PolycodePlayer.cpp
        Source/PolycodeCocoaPlayer.mm
        Platform/Darwin/Standalone/main.m
        Platform/Darwin/Standalone/StandalonePlayerAppDelegate.mm
	../../Core/Contents/PolycodeView/Mac\ OS\ X/PolycodeView.mm
    )
    SET(polycodeplayerstandalone_HDRS 
        Include/PolycodePlayer.h
        Include/PolycodeCocoaPlayer.h
        Platform/Darwin/Standalone/StandalonePlayerAppDelegate.h
	../../Core/Contents/PolycodeView/Mac\ OS\ X/PolycodeView.h
    )
  
    INCLUDE_DIRECTORIES(Include)
    
#    IF(POLYCODE_BUILD_SHARED)
#        ADD_EXECUTABLE(PolycodePlayer MACOSX_BUNDLE ${polycodeplayer_SRCS} ${polycodeplayer_HDRS})
#        TARGET_LINK_LIBRARIES(PolycodePlayer Polycore PolycodeLua ${LUA_LIBRARY})
#    ENDIF(POLYCODE_BUILD_SHARED)

find_program(IBTOOL ibtool HINTS "/usr/bin" "${OSX_DEVELOPER_ROOT}/usr/bin")
if (${IBTOOL} STREQUAL "IBTOOL-NOTFOUND")
  message(SEND_ERROR "ibtool can not be found and is needed to compile the .xib files. It should have been installed with 
                    the Apple developer tools. The default system paths were searched in addition to ${OSX_DEVELOPER_ROOT}/usr/bin")
endif()


	set (PolycodePlayer_XIBS
		MainMenu
		MyDocument
	)

	set (PolycodePlayerStandalone_XIBS
		MainMenu
	)

    ADD_EXECUTABLE(PolycodePlayer MACOSX_BUNDLE ${polycodeplayer_SRCS} ${polycodeplayer_HDRS})
    set_target_properties( PolycodePlayer PROPERTIES MACOSX_BUNDLE_INFO_PLIST ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/Info.plist )

    ADD_EXECUTABLE(StandalonePlayer MACOSX_BUNDLE ${polycodeplayerstandalone_SRCS} ${polycodeplayerstandalone_HDRS})
    set_target_properties( StandalonePlayer PROPERTIES MACOSX_BUNDLE_INFO_PLIST ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/Standalone/Info.plist )

    TARGET_LINK_LIBRARIES(PolycodePlayer 
        Polycore 
        PolycodeLua 
        "-framework Cocoa"
        "-framework IOKit"
        ${LUA_LIBRARY}
        ${OPENGL_LIBRARIES}
        ${OPENAL_LIBRARY}
        ${PNG_LIBRARIES}
        ${FREETYPE_LIBRARIES}
        ${PHYSFS_LIBRARY}
        ${OGG_LIBRARY}
        ${VORBIS_LIBRARY}
        ${VORBISFILE_LIBRARY}
     )

    TARGET_LINK_LIBRARIES(StandalonePlayer 
        Polycore 
        PolycodeLua 
        "-framework Cocoa"
        "-framework IOKit"
        ${LUA_LIBRARY}
        ${OPENGL_LIBRARIES}
        ${OPENAL_LIBRARY}
        ${PNG_LIBRARIES}
        ${FREETYPE_LIBRARIES}
        ${PHYSFS_LIBRARY}
        ${OGG_LIBRARY}
        ${VORBIS_LIBRARY}
        ${VORBISFILE_LIBRARY}
     )

	add_custom_command (TARGET PolycodePlayer PRE_BUILD
                      COMMAND mkdir -p ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources)
	add_custom_command (TARGET PolycodePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/app_file_icon.icns ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources)
	add_custom_command (TARGET PolycodePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/player_icon.icns ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources)
	add_custom_command (TARGET PolycodePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Assets/Default\ asset\ pack/default.pak ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources)
	add_custom_command (TARGET PolycodePlayer PRE_BUILD
                      COMMAND cp -R ${Polycode_SOURCE_DIR}/Bindings/Contents/LUA/API ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources)

	add_custom_command (TARGET StandalonePlayer PRE_BUILD
                      COMMAND mkdir -p ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources)
	add_custom_command (TARGET StandalonePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/player_icon.icns ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources)
	add_custom_command (TARGET StandalonePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Assets/Default\ asset\ pack/default.pak ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources)
	add_custom_command (TARGET StandalonePlayer PRE_BUILD
                      COMMAND cp -R ${Polycode_SOURCE_DIR}/Bindings/Contents/LUA/API ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources)
	add_custom_command (TARGET StandalonePlayer PRE_BUILD
                      COMMAND cp ${Polycode_SOURCE_DIR}/Assets/SamplePolyapp/main.polyapp ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources)


foreach(xib ${PolycodePlayer_XIBS})
  add_custom_command (TARGET PolycodePlayer POST_BUILD 
                      COMMAND ${IBTOOL} --errors --warnings --notices --output-format human-readable-text 
                              --compile ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/PolycodePlayer.app/Contents/Resources/${xib}.nib 
                             ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/${xib}.xib
                      COMMENT "Compiling ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/${xib}.xib")

endforeach()


foreach(xib ${PolycodePlayerStandalone_XIBS})
  add_custom_command (TARGET StandalonePlayer POST_BUILD 
                      COMMAND ${IBTOOL} --errors --warnings --notices --output-format human-readable-text 
                              --compile ${PROJECT_BINARY_DIR}/Player/Contents/\${CONFIGURATION}/StandalonePlayer.app/Contents/Resources/${xib}.nib 
                             ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/Standalone/${xib}.xib
                      COMMENT "Compiling ${Polycode_SOURCE_DIR}/Player/Contents/Platform/Darwin/Standalone/${xib}.xib")

endforeach()

ENDIF(MSVC)

IF(POLYCODE_INSTALL_PLAYER)
#    IF(POLYCODE_BUILD_SHARED)
#        # install player
#        INSTALL(TARGETS PolycodePlayer_dynamic
#            DESTINATION ${POLYCODE_RELEASE_DIR}/Player)
#    ENDIF()
#    IF(POLYCODE_BUILD_STATIC)
    INSTALL(TARGETS PolycodePlayer 
        DESTINATION Player)

    INSTALL(TARGETS StandalonePlayer 
        DESTINATION Player)

#    ENDIF()
ENDIF(POLYCODE_INSTALL_PLAYER)