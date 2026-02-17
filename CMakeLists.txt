cmake_minimum_required(VERSION 3.25 FATAL_ERROR)

project(SmartLoopCapture VERSION 0.1.0)

set(CMAKE_CXX_STANDARD 23)

# Build for Apple Silicon only
set(CMAKE_OSX_ARCHITECTURES "arm64" CACHE STRING "" FORCE)

# macOS 10.11 is the minimum required by JUCE
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.11" CACHE STRING "" FORCE)

# Downloads CPM if not already downloaded. CPM is an easy-to-use package manager nicely integrated with CMake.
set(LIB_DIR ${CMAKE_CURRENT_SOURCE_DIR}/libs)
include(cmake/cpm.cmake)

# Bring in JUCE
CPMAddPackage(
    NAME JUCE
    GIT_TAG 8.0.12
    GITHUB_REPOSITORY juce-framework/JUCE
    SOURCE_DIR ${LIB_DIR}/juce
)

# Bring in googletest
CPMAddPackage(
    NAME GOOGLETEST
    GITHUB_REPOSITORY google/googletest
    VERSION 1.17.0
    SOURCE_DIR ${LIB_DIR}/googletest
    OPTIONS "INSTALL_GTEST OFF" "gtest_force_shared_crt ON"
)

# Make sure you include any new source files here
set(SourceFiles
    audio_plugin/source/PluginProcessor.cpp
    audio_plugin/include/PluginProcessor.h
    audio_plugin/source/PluginEditor.cpp
    audio_plugin/include/PluginEditor.h
)

juce_add_plugin(${PROJECT_NAME}
    COMPANY_NAME "JasonRhee"
    IS_SYNTH FALSE
    NEEDS_MIDI_INPUT FALSE
    NEEDS_MIDI_OUTPUT FALSE
    PLUGIN_MANUFACTURER_CODE Jrhe
    PLUGIN_CODE Slcp
    FORMATS Standalone
    PRODUCT_NAME "SmartLoopCapture"
    VERSION 0.1.0
    COPY_PLUGIN_AFTER_BUILD FALSE
)

# How we want our SourceFiles to appear in our IDE
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SourceFiles})

# Make the SourceFiles buildable
target_sources(${PROJECT_NAME} PRIVATE ${SourceFiles})

target_include_directories(${PROJECT_NAME}
    PRIVATE
        audio_plugin/include
)

# These are some toggleable options from the JUCE CMake API
target_compile_definitions(${PROJECT_NAME}
    PUBLIC
        JUCE_WEB_BROWSER=0
        JUCE_USE_CURL=0
        JUCE_VST3_CAN_REPLACE_VST2=0
        JUCE_PLUGIN_NAME="${PROJECT_NAME}"
)

# JUCE libraries to bring into our project
target_link_libraries(${PROJECT_NAME}
    PUBLIC
        juce::juce_audio_basics
        juce::juce_audio_devices
        juce::juce_audio_formats
        juce::juce_audio_processors
        juce::juce_audio_utils
        juce::juce_core
        juce::juce_data_structures
        juce::juce_events
        juce::juce_graphics
        juce::juce_gui_basics
        juce::juce_gui_extra
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags
)

enable_testing()
add_subdirectory(test)
