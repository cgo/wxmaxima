find_package(wxWidgets REQUIRED std xml html adv aui core net base richtext)

include(${wxWidgets_USE_FILE})

file(GLOB SOURCE_FILES *.cpp *.h)


if(WIN32)
    include_directories(${CMAKE_SOURCE_DIR}/data/winrc)
    set(SOURCE_FILES Resources.rc ../data/winrc/wxmaxima.manifest ${SOURCE_FILES})
endif()

# We put Setup.h into binary dir
include_directories("${CMAKE_CURRENT_BINARY_DIR}")

if(WIN32)
    add_executable(wxmaxima WIN32 ${SOURCE_FILES})
elseif(APPLE)
    add_executable(wxmaxima ${SOURCE_FILES})
    set_target_properties(wxmaxima PROPERTIES
              RUNTIME_OUTPUT_DIRECTORY_DEBUG   ${CMAKE_BINARY_DIR}/wxMaxima.app/Contents/MacOS
              RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/wxMaxima.app/Contents/MacOS)
    add_dependencies(wxmaxima wxMaxima.app)
else()
    add_executable(wxmaxima ${SOURCE_FILES})
endif()

target_link_libraries(wxmaxima ${wxWidgets_LIBRARIES})

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Setup.h.cin ${CMAKE_CURRENT_BINARY_DIR}/Setup.h)

if(WIN32)
    install(TARGETS wxmaxima RUNTIME DESTINATION wxMaxima)
else()
    install(TARGETS wxmaxima RUNTIME DESTINATION bin)
endif()

# OSX binary folders
if(APPLE)
    # DEFINE FILES
    file(GLOB RESOURCES_FILES
            ${CMAKE_BINARY_DIR}/data/wxmathml.lisp
            ${CMAKE_SOURCE_DIR}/test/testbench_simple.wxmx
            ${CMAKE_SOURCE_DIR}/data/wxmaxima.png
            ${CMAKE_SOURCE_DIR}/data/wxmaxima.svg
            ${CMAKE_SOURCE_DIR}/data/autocomplete.txt
            ${CMAKE_SOURCE_DIR}/data/tips*.txt)
    file(GLOB RESOURCES_FILES_OSX
            ${CMAKE_SOURCE_DIR}/art/wxmac.icns
            ${CMAKE_SOURCE_DIR}/art/wxmac-doc.icns
            ${CMAKE_SOURCE_DIR}/art/wxmac-doc-wxm.icns
            ${CMAKE_SOURCE_DIR}/art/wxmac-doc-wxmx.icns)
    file(GLOB HELP_FILES
            ${CMAKE_SOURCE_DIR}/info/*.html
            ${CMAKE_SOURCE_DIR}/info/*.jpg
            ${CMAKE_SOURCE_DIR}/info/*.png
            ${CMAKE_SOURCE_DIR}/info/wxmaxima.hhp)
    file(GLOB CONTENT_FILES
            ${CMAKE_BINARY_DIR}/data/Info.plist
            ${CMAKE_SOURCE_DIR}/data/PkgInfo)
    file(GLOB TOOLBAR_FILES
            ${CMAKE_SOURCE_DIR}/art/toolbar/*.png)
    file(GLOB STATUSBAR_FILES
            ${CMAKE_SOURCE_DIR}/art/statusbar/*.png)
    file(GLOB CONFIG_FILES
            ${CMAKE_SOURCE_DIR}/art/config/*.png)
    file(GLOB FONT_FILES
            ${CMAKE_SOURCE_DIR}/data/fonts/*.ttf)

    if(APPLE)
        # DEFINE DIRECTORIES
        set(APP_DIR ${CMAKE_BINARY_DIR}/wxMaxima.app/Contents)
        set(APP_MACOSX_DIR ${APP_DIR}/MacOS)
        set(APP_RESOURCES_DIR ${APP_DIR}/Resources)
        set(APP_HELP_DIR ${APP_RESOURCES_DIR}/help)
        set(APP_FONTS_DIR ${APP_RESOURCES_DIR}/fonts)
        # CUSTOM TARGET
        add_custom_target(wxMaxima.app)
        add_custom_command(TARGET wxMaxima.app PRE_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_MACOSX_DIR})
        add_custom_command(TARGET wxMaxima.app PRE_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_HELP_DIR})
        add_custom_command(TARGET wxMaxima.app PRE_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_RESOURCES_DIR}/toolbar)
        add_custom_command(TARGET wxMaxima.app PRE_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_RESOURCES_DIR}/statusbar)
        add_custom_command(TARGET wxMaxima.app PRE_BUILD
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_RESOURCES_DIR}/config)
        add_custom_command(TARGET wxMaxima.app
                COMMAND ${CMAKE_COMMAND} -E make_directory ${APP_FONTS_DIR})
        foreach(f ${RESOURCE_FILES})
            add_custom_command(TARGET wxMaxima.app
                COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR})
        endforeach()
        foreach(f ${RESOURCES_FILES_OSX})
            add_custom_command(TARGET wxMaxima.app
                COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR})
        endforeach()
        foreach(f ${CONTENT_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_DIR})
        endforeach()
        foreach(f ${STATUSBAR_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR}/statusbar)
        endforeach()
        foreach(f ${TOOLBAR_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR}/toolbar)
        endforeach()
        foreach(f ${CONFIG_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR}/config)
        endforeach()
        foreach(f ${RESOURCES_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_RESOURCES_DIR})
        endforeach()
        foreach(f ${HELP_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f}  ${APP_HELP_DIR})
        endforeach()
        foreach(f ${FONT_FILES})
            add_custom_command(TARGET wxMaxima.app
                    COMMAND ${CMAKE_COMMAND} -E copy ${f} ${APP_FONTS_DIR})
        endforeach()
    endif()
endif()

