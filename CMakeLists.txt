cmake_minimum_required(VERSION 3.7.0)

project(AttorneyOnline VERSION 2.11.0.0 LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

option(AO_BUILD_TESTS "Build test programs" ON)
option(AO_ENABLE_DISCORD_RPC "Enable Discord Rich Presence" ON)

find_package(QT NAMES Qt6)
find_package(Qt6 REQUIRED COMPONENTS Core Gui Network Widgets Concurrent WebSockets UiTools)
if(ANDROID)
  # Needed for private/qandroidextras_p.h (runtime storage permission requests)
  find_package(Qt6 REQUIRED COMPONENTS CorePrivate)
endif()

qt_add_executable(Attorney_Online
  src/aoapplication.cpp
  src/aoapplication.h
  src/aoblipplayer.cpp
  src/aoblipplayer.h
  src/aobutton.cpp
  src/aobutton.h
  src/aocharbutton.cpp
  src/aocharbutton.h
  src/aoclocklabel.cpp
  src/aoclocklabel.h
  src/aoemotebutton.cpp
  src/aoemotebutton.h
  src/aoemotepreview.cpp
  src/aoemotepreview.h
  src/aoevidencebutton.cpp
  src/aoevidencebutton.h
  src/aoevidencedisplay.cpp
  src/aoevidencedisplay.h
  src/aoimage.cpp
  src/aoimage.h
  src/animationlayer.cpp
  src/animationlayer.h
  src/animationloader.h
  src/animationloader.cpp
  src/aomusicplayer.cpp
  src/aomusicplayer.h
  src/aopacket.cpp
  src/aopacket.h
  src/aosfxplayer.cpp
  src/aosfxplayer.h
  src/aotextarea.cpp
  src/aotextarea.h
  src/aotextboxwidgets.cpp
  src/aotextboxwidgets.h
  src/aoutils.cpp
  src/aoutils.h
  src/charselect.cpp
  src/chatlogpiece.cpp
  src/chatlogpiece.h
  src/courtroom.cpp
  src/courtroom.h
  src/datatypes.h
  src/debug_functions.cpp
  src/debug_functions.h
  src/demoserver.cpp
  src/demoserver.h
  src/discord_rich_presence.cpp
  src/discord_rich_presence.h
  src/emotes.cpp
  src/eventfilters.cpp
  src/eventfilters.h
  src/evidence.cpp
  src/file_functions.cpp
  src/file_functions.h
  src/gui_utils.h
  src/hardware_functions.cpp
  src/hardware_functions.h
  src/lobby.cpp
  src/lobby.h
  src/main.cpp
  src/network/websocketconnection.cpp
  src/network/websocketconnection.h
  src/networkmanager.cpp
  src/networkmanager.h
  src/options.cpp
  src/options.h
  src/packet_distribution.cpp
  src/path_functions.cpp
  src/scrolltext.cpp
  src/scrolltext.h
  src/serverdata.cpp
  src/serverdata.h
  src/text_file_functions.cpp
  src/widgets/aooptionsdialog.cpp
  src/widgets/aooptionsdialog.h
  src/widgets/direct_connect_dialog.cpp
  src/widgets/direct_connect_dialog.h
  src/widgets/server_editor_dialog.cpp
  src/widgets/server_editor_dialog.h
  data.qrc
  src/widgets/playerlistwidget.h src/widgets/playerlistwidget.cpp
  src/widgets/moderator_dialog.h src/widgets/moderator_dialog.cpp
  src/screenslidetimer.h src/screenslidetimer.cpp
  src/moderation_functions.h src/moderation_functions.cpp
  src/network/serverinfo.h src/network/serverinfo.cpp
)

set_target_properties(Attorney_Online PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/bin")

if(ANDROID)
  set_property(TARGET Attorney_Online PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
  # Prebuilt BASS/BASSOPUS .so files are staged per-ABI by CI into lib/<ANDROID_ABI>/
  set_property(TARGET Attorney_Online PROPERTY QT_ANDROID_EXTRA_LIBS
    "${CMAKE_CURRENT_SOURCE_DIR}/lib/${ANDROID_ABI}/libbass.so"
    "${CMAKE_CURRENT_SOURCE_DIR}/lib/${ANDROID_ABI}/libbassopus.so")
endif()

if(WIN32)
  if(CMAKE_BUILD_TYPE STREQUAL "Release")
    set_property(TARGET Attorney_Online PROPERTY WIN32_EXECUTABLE true)
    set(APP_ICON_RESOURCE_WINDOWS "${CMAKE_CURRENT_SOURCE_DIR}/data/logo-client.rc")
    target_sources(Attorney_Online PRIVATE ${APP_ICON_RESOURCE_WINDOWS})
  endif()
endif()

target_include_directories(Attorney_Online PRIVATE src lib)
if(ANDROID)
  target_link_directories(Attorney_Online PRIVATE "lib/${ANDROID_ABI}")
else()
  target_link_directories(Attorney_Online PRIVATE lib)
endif()
target_link_libraries(Attorney_Online PRIVATE
  Qt${QT_VERSION_MAJOR}::Core
  Qt${QT_VERSION_MAJOR}::Gui
  Qt${QT_VERSION_MAJOR}::Network
  Qt${QT_VERSION_MAJOR}::Widgets
  Qt${QT_VERSION_MAJOR}::Concurrent
  Qt${QT_VERSION_MAJOR}::WebSockets
  Qt${QT_VERSION_MAJOR}::UiTools
  bass
  bassopus
)

if(AO_ENABLE_DISCORD_RPC)
  target_compile_definitions(Attorney_Online PRIVATE AO_ENABLE_DISCORD_RPC)
  target_link_libraries(Attorney_Online PRIVATE discord-rpc)
endif()

if(ANDROID)
  target_link_libraries(Attorney_Online PRIVATE Qt${QT_VERSION_MAJOR}::CorePrivate)
endif()

if(AO_BUILD_TESTS)
  add_subdirectory(test)
endif()

set_target_properties(Attorney_Online PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY $<1:${CMAKE_CURRENT_LIST_DIR}/bin>
        RUNTIME_OUTPUT_DIRECTORY $<1:${CMAKE_CURRENT_LIST_DIR}/bin>)
