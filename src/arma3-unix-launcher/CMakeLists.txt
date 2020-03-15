set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

find_package(Qt5Widgets CONFIG REQUIRED)
find_package(Qt5Svg CONFIG REQUIRED)

file(GLOB SOURCES *.cpp *.h *.hpp *.qrc *.ui)

add_executable(arma3-unix-launcher MACOSX_BUNDLE ${SOURCES})
target_link_libraries(arma3-unix-launcher PRIVATE
    arma3-unix-launcher-library
    argparse::argparse
    fmt::fmt
    nlohmann::json
    Qt5::Widgets
    Qt5::Svg)
#install(TARGETS arma3-unix-launcher DESTINATION bin)
install(TARGETS arma3-unix-launcher
        BUNDLE DESTINATION . COMPONENT Runtime
        RUNTIME DESTINATION bin COMPONENT Runtime)

if (APPLE)    
    find_package(Qt5Widgets CONFIG REQUIRED)
    get_target_property(WIDGETS_LOCATION Qt5::Widgets IMPORTED_LOCATION)
    get_filename_component(QT_BIN_DIR "${WIDGETS_LOCATION}" DIRECTORY)
    find_program(MACDEPLOYQT_EXECUTABLE macdeployqt HINTS "${QT_BIN_DIR}")
    
    add_custom_command(TARGET arma3-unix-launcher POST_BUILD
        COMMAND "${MACDEPLOYQT_EXECUTABLE}"
            "$<TARGET_FILE_DIR:arma3-unix-launcher>/../.."
            -always-overwrite
        COMMENT "Running macdeployqt...")
    
    set(CPACK_GENERATOR "DragNDrop")
    include(CPack)
endif()