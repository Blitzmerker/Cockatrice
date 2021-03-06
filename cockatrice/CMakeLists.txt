# CMakeLists for cockatrice directory
#
# provides the cockatrice binary

PROJECT(cockatrice)

SET(cockatrice_SOURCES
    src/abstractcounter.cpp 
    src/counter_general.cpp 
    src/dlg_creategame.cpp 
    src/dlg_filter_games.cpp
    src/dlg_connect.cpp 
    src/dlg_create_token.cpp 
    src/dlg_edit_tokens.cpp
    src/abstractclient.cpp 
    src/remoteclient.cpp 
    src/main.cpp 
    src/window_main.cpp 
    src/gamesmodel.cpp 
    src/player.cpp 
    src/playertarget.cpp 
    src/cardzone.cpp 
    src/selectzone.cpp 
    src/cardlist.cpp 
    src/abstractcarditem.cpp 
    src/carditem.cpp 
    src/tablezone.cpp 
    src/handzone.cpp 
    src/handcounter.cpp 
    src/carddatabase.cpp 
    src/keysignals.cpp
    src/gameview.cpp 
    src/gameselector.cpp 
    src/decklistmodel.cpp 
    src/deck_loader.cpp 
    src/dlg_load_deck_from_clipboard.cpp 
    src/dlg_load_remote_deck.cpp 
    src/cardinfowidget.cpp 
    src/cardframe.cpp
    src/cardinfopicture.cpp
    src/cardinfotext.cpp
    src/filterbuilder.cpp
    src/cardfilter.cpp
    src/filtertreemodel.cpp
    src/filtertree.cpp
    src/messagelogwidget.cpp 
    src/zoneviewzone.cpp 
    src/zoneviewwidget.cpp 
    src/pilezone.cpp 
    src/stackzone.cpp 
    src/carddragitem.cpp 
    src/carddatabasemodel.cpp 
    src/setsmodel.cpp 
    src/window_sets.cpp 
    src/abstractgraphicsitem.cpp 
    src/abstractcarddragitem.cpp 
    src/dlg_settings.cpp 
    src/dlg_cardsearch.cpp 
    src/phasestoolbar.cpp 
    src/gamescene.cpp 
    src/arrowitem.cpp 
    src/arrowtarget.cpp 
    src/tab.cpp 
    src/tab_server.cpp 
    src/tab_room.cpp 
    src/tab_message.cpp 
    src/tab_game.cpp 
    src/tab_deck_storage.cpp 
    src/tab_replays.cpp 
    src/tab_supervisor.cpp 
    src/tab_admin.cpp 
    src/tab_userlists.cpp 
    src/tab_deck_editor.cpp
    src/replay_timeline_widget.cpp
    src/deckstats_interface.cpp
    src/chatview.cpp 
    src/userlist.cpp 
    src/userinfobox.cpp 
    src/user_context_menu.cpp
    src/remotedecklist_treewidget.cpp 
    src/remotereplaylist_treewidget.cpp 
    src/deckview.cpp 
    src/playerlistwidget.cpp 
    src/pixmapgenerator.cpp 
    src/settingscache.cpp 
    src/localserver.cpp 
    src/localserverinterface.cpp 
    src/localclient.cpp 
    src/priceupdater.cpp 
    src/qt-json/json.cpp
    src/soundengine.cpp
    src/pending_command.cpp
    ${VERSION_STRING_CPP}
)

if (UNIX AND NOT APPLE)
    set_source_files_properties(src/main.cpp PROPERTIES COMPILE_FLAGS -DTRANSLATION_PATH=\\"${CMAKE_INSTALL_PREFIX}/share/cockatrice/translations\\")
endif (UNIX AND NOT APPLE)

set(cockatrice_RESOURCES cockatrice.qrc)
FILE(GLOB cockatrice_TS "${CMAKE_CURRENT_SOURCE_DIR}/translations/*.ts")

IF(UPDATE_TRANSLATIONS)
    FILE(GLOB_RECURSE translate_cockatrice_SRCS ${CMAKE_SOURCE_DIR}/cockatrice/src/*.cpp)
    FILE(GLOB_RECURSE translate_common_SRCS ${CMAKE_CURRENT_SOURCE_DIR}/common/*.cpp)
    SET(translate_SRCS ${translate_cockatrice_SRCS} ${translate_common_SRCS})
ENDIF(UPDATE_TRANSLATIONS)

if(WIN32)
    set(cockatrice_SOURCES ${cockatrice_SOURCES} cockatrice.rc)
endif(WIN32)

if(APPLE)
    set(MACOSX_BUNDLE_ICON_FILE appicon.icns)
    set_source_files_properties(${CMAKE_CURRENT_SOURCE_DIR}/resources/appicon.icns PROPERTIES MACOSX_PACKAGE_LOCATION Resources)
    set(cockatrice_SOURCES ${cockatrice_SOURCES} ${CMAKE_CURRENT_SOURCE_DIR}/resources/appicon.icns)
ENDIF(APPLE)

set(COCKATRICE_LIBS)

# Qt4 stuff
if(Qt4_FOUND)
    if (NOT QT_QTMULTIMEDIA_FOUND)
        FIND_PACKAGE(QtMobility REQUIRED)
    endif()

    SET(QT_USE_QTNETWORK TRUE)
    SET(QT_USE_QTMULTIMEDIA TRUE)
    SET(QT_USE_QTXML TRUE)
    SET(QT_USE_QTSVG TRUE)

    # Include directories
    INCLUDE(${QT_USE_FILE})
    INCLUDE_DIRECTORIES(${QT_INCLUDES})
    INCLUDE_DIRECTORIES(${QT_MOBILITY_INCLUDE_DIR})
    INCLUDE_DIRECTORIES(${QT_MOBILITY_MULTIMEDIAKIT_INCLUDE_DIR})
    LIST(APPEND COCKATRICE_LIBS ${QT_LIBRARIES})
    LIST(APPEND COCKATRICE_LIBS ${QT_QTMAIN_LIBRARY})
    LIST(APPEND COCKATRICE_LIBS ${QT_MOBILITY_MULTIMEDIAKIT_LIBRARY})

    # Let cmake chew Qt4's translations and resource files
    # Note: header files are MOC-ed automatically by cmake
    IF(UPDATE_TRANSLATIONS)
        QT4_CREATE_TRANSLATION(cockatrice_QM ${translate_SRCS} ${cockatrice_TS})
    ELSE(UPDATE_TRANSLATIONS)
        QT4_ADD_TRANSLATION(cockatrice_QM ${cockatrice_TS})
    ENDIF(UPDATE_TRANSLATIONS)

    QT4_ADD_RESOURCES(cockatrice_RESOURCES_RCC ${cockatrice_RESOURCES})
endif()

# qt5 stuff
if(Qt5Widgets_FOUND)
    include_directories(${Qt5Widgets_INCLUDE_DIRS})
    list(APPEND COCKATRICE_LIBS Widgets)

    # QtNetwork
    find_package(Qt5Network)
    if(Qt5Network_FOUND)
        include_directories(${Qt5Network_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS Network)
    endif()

    # QtMultimedia
    find_package(Qt5Multimedia)
    if(Qt5Multimedia_FOUND)
        include_directories(${Qt5Multimedia_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS Multimedia)
    endif()

    # QtPrinter
    find_package(Qt5PrintSupport)
    if(Qt5PrintSupport_FOUND)
        include_directories(${Qt5PrintSupport_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS PrintSupport)
    endif()

    # QtXml
    find_package(Qt5Xml)
    if(Qt5Xml_FOUND)
        include_directories(${Qt5Xml_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS Xml)
    endif()

    # QtSvg
    find_package(Qt5Svg)
    if(Qt5Svg_FOUND)
        include_directories(${Qt5Svg_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS Svg)
    endif()

    # Qt5LinguistTools
    find_package(Qt5LinguistTools)
    if(Qt5LinguistTools_FOUND)
        include_directories(${Qt5LinguistTools_INCLUDE_DIRS})
        list(APPEND COCKATRICE_LIBS LinguistTools)
    endif()

    # Let cmake chew Qt5's translations and resource files
    # Note: header files are MOC-ed automatically by cmake
    IF(UPDATE_TRANSLATIONS)
        QT5_CREATE_TRANSLATION(cockatrice_QM ${translate_SRCS} ${cockatrice_TS})
    ELSE()
        QT5_ADD_TRANSLATION(cockatrice_QM ${cockatrice_TS})
    ENDIF()

    QT5_ADD_RESOURCES(cockatrice_RESOURCES_RCC ${cockatrice_RESOURCES})

    # guess plugins and libraries directory
    set(QT_PLUGINS_DIR "${Qt5Widgets_DIR}/../../../plugins")
    get_target_property(QT_LIBRARY_DIR Qt5::Core LOCATION)
    get_filename_component(QT_LIBRARY_DIR ${QT_LIBRARY_DIR} PATH)
endif()

# Declare path variables
set(ICONDIR share/icons CACHE STRING "icon dir")
set(DESKTOPDIR share/applications CACHE STRING "desktop file destination")

# Include directories
INCLUDE_DIRECTORIES(../common)
INCLUDE_DIRECTORIES(${PROTOBUF_INCLUDE_DIR})
INCLUDE_DIRECTORIES(${CMAKE_BINARY_DIR}/common)
INCLUDE_DIRECTORIES(${CMAKE_CURRENT_BINARY_DIR})

# Build cockatrice binary and link it
ADD_EXECUTABLE(cockatrice WIN32 MACOSX_BUNDLE ${cockatrice_SOURCES} ${cockatrice_QM} ${cockatrice_RESOURCES_RCC} ${cockatrice_MOC_SRCS})

if(Qt4_FOUND)
    if(MSVC) 
        set(QT_USE_QTMAIN true) 
    endif() 
    TARGET_LINK_LIBRARIES(cockatrice cockatrice_common ${COCKATRICE_LIBS})
endif()
if(Qt5Widgets_FOUND)
    if(MSVC)
        TARGET_LINK_LIBRARIES(cockatrice cockatrice_common Qt5::WinMain)
    else()
        TARGET_LINK_LIBRARIES(cockatrice cockatrice_common)
    endif()
    qt5_use_modules(cockatrice ${COCKATRICE_LIBS})
endif()

if(UNIX)
    if(APPLE)
        set(MACOSX_BUNDLE_INFO_STRING "${PROJECT_NAME}")
        set(MACOSX_BUNDLE_GUI_IDENTIFIER "com.cockatrice.${PROJECT_NAME}")
        set(MACOSX_BUNDLE_LONG_VERSION_STRING "${PROJECT_NAME}-${PROJECT_VERSION}")
        set(MACOSX_BUNDLE_BUNDLE_NAME ${PROJECT_NAME})
        set(MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION})
        set(MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION})

        INSTALL(TARGETS cockatrice BUNDLE DESTINATION ./)
        INSTALL(FILES ${cockatrice_QM} DESTINATION ./cockatrice.app/Contents/Resources/translations)
    else()
        # Assume linux
        INSTALL(TARGETS cockatrice RUNTIME DESTINATION bin/)
        INSTALL(FILES ${CMAKE_CURRENT_SOURCE_DIR}/resources/cockatrice.png DESTINATION ${ICONDIR}/hicolor/48x48/apps)
        INSTALL(FILES ${CMAKE_CURRENT_SOURCE_DIR}/resources/cockatrice.svg DESTINATION ${ICONDIR}/hicolor/scalable/apps)
        INSTALL(FILES ${CMAKE_CURRENT_SOURCE_DIR}/cockatrice.desktop DESTINATION ${DESKTOPDIR})
        INSTALL(FILES ${cockatrice_QM} DESTINATION share/cockatrice/translations)
    endif()
elseif(WIN32)
        INSTALL(TARGETS cockatrice RUNTIME DESTINATION ./)
        INSTALL(FILES ${cockatrice_QM} DESTINATION ./translations)
endif()

if(APPLE)
    # these needs to be relative to CMAKE_INSTALL_PREFIX
    set(plugin_dest_dir cockatrice.app/Contents/Plugins)
    set(qtconf_dest_dir cockatrice.app/Contents/Resources)

    # note: no codecs in qt5
    # note: phonon_backend => audio | mediaservice
    # note: needs platform on osx

    if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        install(DIRECTORY "${QT_PLUGINS_DIR}/" DESTINATION ${plugin_dest_dir} COMPONENT Runtime
            FILES_MATCHING REGEX "(audio|codecs|iconengines|imageformats|mediaservice|phonon_backend|platforms)/.*_debug\\.dylib")
    else()
        install(DIRECTORY "${QT_PLUGINS_DIR}/" DESTINATION ${plugin_dest_dir} COMPONENT Runtime
            FILES_MATCHING REGEX "(audio|codecs|iconengines|imageformats|mediaservice|phonon_backend|platforms)/.*\\.dylib"
            REGEX ".*_debug\\.dylib" EXCLUDE)
    endif()

    install(CODE "
        file(WRITE \"\${CMAKE_INSTALL_PREFIX}/${qtconf_dest_dir}/qt.conf\" \"[Paths]
Plugins = Plugins
Translations = Resources/translations
Data = Resources\")
    " COMPONENT Runtime)

    install(CODE "
        file(GLOB_RECURSE QTPLUGINS
            \"\${CMAKE_INSTALL_PREFIX}/${plugin_dest_dir}/*.dylib\")
        set(BU_CHMOD_BUNDLE_ITEMS ON)
        include(BundleUtilities)
        fixup_bundle(\"\${CMAKE_INSTALL_PREFIX}/cockatrice.app\" \"\${QTPLUGINS}\" \"${QT_LIBRARY_DIR}\")
        " COMPONENT Runtime)
endif()

if(WIN32)
    # these needs to be relative to CMAKE_INSTALL_PREFIX
    set(plugin_dest_dir Plugins)
    set(qtconf_dest_dir .)

    # note: no codecs in qt5
    # note: phonon_backend => audio | mediaservice
    # note: needs platform on osx

    if (CMAKE_BUILD_TYPE STREQUAL "Debug")
        install(DIRECTORY "${QT_PLUGINS_DIR}/" DESTINATION ${plugin_dest_dir} COMPONENT Runtime
            FILES_MATCHING REGEX "(audio|codecs|iconengines|imageformats|mediaservice|phonon_backend|platforms)/.*d\\.dll")
    else()
        install(DIRECTORY "${QT_PLUGINS_DIR}/" DESTINATION ${plugin_dest_dir} COMPONENT Runtime
            FILES_MATCHING REGEX "(audio|codecs|iconengines|imageformats|mediaservice|phonon_backend|platforms)/.*[^d]\\.dll")
    endif()

    install(CODE "
        file(WRITE \"\${CMAKE_INSTALL_PREFIX}/${qtconf_dest_dir}/qt.conf\" \"[Paths]
Plugins = Plugins
Translations = Resources/translations
Data = Resources\")
    " COMPONENT Runtime)

    install(CODE "
        file(GLOB_RECURSE QTPLUGINS
            \"\${CMAKE_INSTALL_PREFIX}/${plugin_dest_dir}/*.dll\")
        set(BU_CHMOD_BUNDLE_ITEMS ON)
        include(BundleUtilities)
        fixup_bundle(\"\${CMAKE_INSTALL_PREFIX}/cockatrice.exe\" \"\${QTPLUGINS}\" \"${QT_LIBRARY_DIR}\")
        " COMPONENT Runtime)

    if(WIN32SSLRUNTIME_FOUND)
        install(FILES ${WIN32SSLRUNTIME_LIBRARIES} DESTINATION ./)
    endif()
endif()