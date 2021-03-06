
CONFIGURE_FILE("${CMAKE_CURRENT_SOURCE_DIR}/include/hydrogen/config.h.in" "${CMAKE_CURRENT_BINARY_DIR}/include/hydrogen/config.h" @ONLY)

FILE(GLOB_RECURSE hydrogen_INCLUDES include/*.h)
FILE(GLOB_RECURSE hydrogen_SOURCES src/*.cpp src/*.cc src/*.c)
LIST(APPEND hydrogen_INCLUDES ${CMAKE_CURRENT_BINARY_DIR}/include/hydrogen/config.h)

ADD_LIBRARY( hydrogen-core-${VERSION} ${H2CORE_LIBRARY_TYPE} ${hydrogen_SOURCES})
INCLUDE_DIRECTORIES( include
    ${CMAKE_SOURCE_DIR}/include                 # regular headers
    ${CMAKE_CURRENT_BINARY_DIR}/include         # generated config.h
    ${QT_INCLUDES}
    ${LIBTAR_INCLUDE_DIRS}
    ${LIBARCHIVE_INCLUDE_DIRS}
    ${LIBSNDFILE_INCLUDE_DIRS}
    ${PULSEAUDIO_INCLUDE_DIRS}
    ${ALSA_INCLUDE_DIRS}
    ${OSS_INCLUDE_DIRS}
    ${JACK_INCLUDE_DIRS}
    ${PORTAUDIO_INCLUDE_DIRS}
    ${PORTMIDI_INCLUDE_DIRS}
    ${COREAUDIO_INCLUDE_DIRS}
    ${COREMIDI_INCLUDE_DIRS}
    ${LASH_INCLUDE_DIRS}
    ${LRDF_INCLUDE_DIRS}
    ${OSC_INCLUDE_DIRS}
    ${RUBBERBAND_INCLUDE_DIRS}
)

TARGET_LINK_LIBRARIES(hydrogen-core-${VERSION}
    ${LIBTAR_LIBRARIES}
    ${LIBARCHIVE_LIBRARIES}
    ${LIBSNDFILE_LIBRARIES}
    ${ALSA_LIBRARIES}
    ${OSS_LIBRARIES}
    ${JACK_LIBRARIES}
    ${PORTAUDIO_LIBRARIES}
    ${PORTMIDI_LIBRARIES}
    ${AUDIOUNIT_LIBRARY}
    ${CORESERVICES_LIBRARY}
    ${COREAUDIO_LIBRARIES}
    ${COREMIDI_LIBRARIES}
    ${PULSEAUDIO_LIBRARIES}
    ${LASH_LIBRARIES}
    ${LRDF_LIBRARIES}
    ${RUBBERBAND_LIBRARIES}
    ${ZLIB_LIBRARIES}
    ${CMAKE_THREAD_LIBS_INIT}
    ${OSC_LIBRARIES}
)

TARGET_LINK_LIBRARIES(hydrogen-core-${VERSION}
	Qt5::Core
	Qt5::Xml
	Qt5::XmlPatterns
)

#SET_TARGET_PROPERTIES(hydrogen-core-${VERSION} PROPERTIES PUBLIC_HEADER   "${hydrogen_INCLUDES}" )
SET_PROPERTY(TARGET hydrogen-core-${VERSION} PROPERTY CXX_STANDARD 14)


IF(WIN32)
    # On Windows we do not want to install import library (.dll.a)
    INSTALL(TARGETS hydrogen-core-${VERSION}
        LIBRARY DESTINATION "${H2_LIB_PATH}"
        RUNTIME DESTINATION "${H2_LIB_PATH}"
    )
ELSE()
    INSTALL(TARGETS hydrogen-core-${VERSION}
        LIBRARY DESTINATION "${H2_LIB_PATH}"
        RUNTIME DESTINATION "${H2_LIB_PATH}"
        ARCHIVE DESTINATION "${H2_LIB_PATH}"
    )
ENDIF()

IF(NOT APPLE AND NOT WIN32)
    INSTALL(DIRECTORY include DESTINATION "${CMAKE_INSTALL_PREFIX}" FILES_MATCHING PATTERN "*.h" PATTERN ".svn" EXCLUDE)
    INSTALL(FILES "${CMAKE_CURRENT_BINARY_DIR}/include/hydrogen/config.h" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/hydrogen" )
ENDIF()
