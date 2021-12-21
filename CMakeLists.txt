cmake_minimum_required(VERSION 3.5)

project(CVParameter VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC_SEARCH_PATHS resources/ui)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_compile_options(-DUNICODE -D_UNICODE /MT /GR-)

find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets REQUIRED)

include_directories(modules include include/ui third_party)

aux_source_directory(src PROJECT_SOURCES01)
aux_source_directory(src/ui PROJECT_SOURCES02)
file(GLOB PROJECT_SOURCES03 include/ui/*.h)

set(PROJECT_SOURCES ${PROJECT_SOURCES01} ${PROJECT_SOURCES02} ${PROJECT_SOURCES03})

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(CVParameter
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET CVParameter APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(CVParameter SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(CVParameter
            ${PROJECT_SOURCES}
        )
    endif()
endif()

target_link_libraries(CVParameter PRIVATE Qt${QT_VERSION_MAJOR}::Widgets)

set_target_properties(CVParameter PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(CVParameter)
endif()