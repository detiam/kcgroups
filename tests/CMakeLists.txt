include(ECMMarkAsTest)

find_package(Qt${QT_MAJOR_VERSION} COMPONENTS Test Quick Qml Widgets)
if(NOT Qt${QT_MAJOR_VERSION}_FOUND)
  message(STATUS "QML/Test Qt${QT_MAJOR_VERSION} components not found, will not build tests")
  return()
endif()

if(QT_MAJOR_VERSION EQUAL 6)
  qt6_add_resources(RESOURCES qml.qrc)
else()
  qt5_add_resources(RESOURCES qml.qrc)
endif()

add_executable(spinner spinner.cpp ${RESOURCES})
target_link_libraries(
    spinner
    Qt${QT_MAJOR_VERSION}::Qml
    Qt${QT_MAJOR_VERSION}::Quick
    Qt${QT_MAJOR_VERSION}::Widgets)

add_executable(foregroundtest foregroundtest.cpp)
ecm_mark_as_test(foregroundtest)
add_dependencies(foregroundtest spinner)
target_link_libraries(
    foregroundtest
    Qt${QT_MAJOR_VERSION}::Test)
