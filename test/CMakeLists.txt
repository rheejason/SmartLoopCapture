enable_testing()

add_executable(AudioPluginTest source/AudioProcessorTest.cpp)

target_sources(AudioPluginTest
    PRIVATE
        ../audio_plugin/source/PluginProcessor.cpp
        ../audio_plugin/source/PluginEditor.cpp
)

target_include_directories(AudioPluginTest
    PRIVATE
        ../audio_plugin/include
)

target_link_libraries(AudioPluginTest
    PRIVATE
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
        GTest::gtest_main
)

target_compile_definitions(AudioPluginTest
    PRIVATE
        JUCE_WEB_BROWSER=0
        JUCE_USE_CURL=0
        JUCE_PLUGIN_NAME="${PROJECT_NAME}"
)

include(GoogleTest)
gtest_discover_tests(AudioPluginTest)
