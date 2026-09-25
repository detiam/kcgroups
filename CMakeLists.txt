cmake_minimum_required(VERSION 3.10)

project(ForegroundBooster)

option(BUILD_WITH_QT6 "Build ForegroundBooster with Qt6/KF6" ON)

if(BUILD_WITH_QT6)
    set(QT_MAJOR_VERSION 6)
    set(KF_MAJOR_VERSION 6)
    find_package(ECM 6.0 REQUIRED NO_MODULE)
else()
    set(QT_MAJOR_VERSION 5)
    set(KF_MAJOR_VERSION 5)
    find_package(ECM 5.73 REQUIRED NO_MODULE)
endif()

set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH})

include(KDEInstallDirs)
include(KDECompilerSettings)
include(KDECMakeSettings)
include(ECMConfiguredInstall)

find_package(Qt${QT_MAJOR_VERSION}DBus REQUIRED NO_MODULE)
find_package(KF${KF_MAJOR_VERSION} REQUIRED COMPONENTS Config DBusAddons)
find_package(KF5CGroups REQUIRED)
find_package(LibTaskManager CONFIG REQUIRED)

if(BUILD_TESTING)
    add_subdirectory(tests)
endif()

add_executable(foreground_booster foregroundbooster.cpp foregroundbooster.h booster_main.cpp)
kconfig_add_kcfg_files(foreground_booster boostersettings.kcfgc)
target_link_libraries(
    foreground_booster
    Qt${QT_MAJOR_VERSION}::DBus
    PW::LibTaskManager
    KF${KF_MAJOR_VERSION}::ConfigCore
    KF${KF_MAJOR_VERSION}::ConfigGui
    KF${KF_MAJOR_VERSION}::DBusAddons
    KF5::CGroups)
install(TARGETS foreground_booster ${KDE_INSTALL_TARGETS_DEFAULT_ARGS})

configure_file(org.kde.foreground-booster.desktop.in ${CMAKE_CURRENT_BINARY_DIR}/org.kde.foreground-booster.desktop @ONLY)
install(FILES  ${CMAKE_CURRENT_BINARY_DIR}/org.kde.foreground-booster.desktop DESTINATION ${KDE_INSTALL_APPDIR})
install(FILES  ${CMAKE_CURRENT_BINARY_DIR}/org.kde.foreground-booster.desktop DESTINATION ${KDE_INSTALL_AUTOSTARTDIR})


ecm_install_configured_files(INPUT plasma-foreground-booster.service.in DESTINATION ${KDE_INSTALL_SYSTEMDUSERUNITDIR})
