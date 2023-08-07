# simple-plugin-gui

Development status: in progress.

A simple audio plugin made with JUCE showing knobs, toggles, switches, and a VU meter.

This plugin includes my subjective and non-exhaustive list of details that make a plugin feel high quality and easy to use, including:
* Proportional resizing
* Double click controls to reset
* Arrow keys to adjust values
* Shift/control while modifying for fine adjustment
* Parameters exposed to host DAW for automation with accurate labels and values
* GUI feedback on mouse hover and select
* Parameter smoothing to prevent pops/clicks

### Notes:
This fork was configured for debugging with the "heavyweight" leak detector:

git clone https://github.com/marclava/simple-plugin-gui.git -b lv2_debug

Clone JUCE :

    git clone --recursive --depth 1 https://github.com/juce-framework/JUCE.git -b develop

In JUCE/modules/juce_audio_plugin_client/LV2/juce_LV2_Client.cpp :

Change the test starting at line 1367 to :

            if (! boundedBlockLength)
            {
                // The host doesn't provide the 'bounded block length' feature
                jassertfalse;
                Logger::outputDebugString("\n:-( The host doesn't provide the 'bounded block length' feature...\n");
                //~ return nullptr;
            } else {
                Logger::outputDebugString("\n:-) The host does provide the 'bounded block length' feature!\n");
            }

Set the location of the JUCE sdk in simple-plugin-gui/src/CMakeLists.txt (line 28)

Compile and install the plugin:

cd simple-plugin-gui/src

mkdir build

cd build

cmake -DCMAKE_BUILD_TYPE=Debug -DJUCE_COPY_PLUGIN_AFTER_BUILD=1 ..

make -j$(nproc)

There should be a new (or updated) plugin here: "$HOME/.lv2/Simple Plugin GUI.lv2"


Try it with jalv:

jalv.gtk https://www.github.com/plugins/simple_plugin_gui

The console shows:

JUCE v7.0.2

😄 The host does provide the 'bounded block length' feature!


It works. Quit jalv; it won't crash.


Now load the plugin with ingen. The console shows:

JUCE v7.0.2
JUCE Assertion failure in juce_LV2_Client.cpp:1370

🙁 The host doesn't provide the 'bounded block length' feature...

It also works (because I disabled the return in the test)

Now quit ingen: you'll see the trace of the leak detector. 

