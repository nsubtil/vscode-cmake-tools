# [EXPERIMENTAL] Target Debugging

Most C and C++ development environments offer some integration with project
configuration that requires awareness of the build system, including the
presence of targets and build artifacts. The experimental Target Debugging
feature offers some such integration with Visual Studio Code for CMake projects
that produce executable targets (including executables used just for testing).

The Target Debugging is meant to make it far simpler for CMake-based projects to
manage their debugging setup within VS Code. There is no need to write a
``launch.json`` file for Target Debugging, and no need to manually specify
the path/architecture/debugger for an executable. All this is determined
automatically by CMake Tools when a target-based debug is invoked.

## NOTICE

This feature is experimental, and may or may not work with all CMake versions.
As such, it is opt-in for now, until the feature's stability can be determined.
To help accelerate this feature's development, please [provide feedback on
the associated GitHub issue here](https://github.com/vector-of-bool/vscode-cmake-tools/issues/37).
Simply leaving a +1 is very useful to see how many people successfully use
the feature! Thanks!

## Enabling Target Debugging

### Telling CMake Tools About the project

CMake Tools needs to know information about your project. It will emit a CMake
module which can be easily included in any CMake project by adding the following
line at the top of the ``CMakeLists.txt`` file, before calling any
``add_library`` or ``add_executable``:

~~~cmake
include(CMakeToolsHelpers OPTIONAL)
~~~

The keyword ``OPTIONAL`` here means that CMake won't complain if it fails to
find the module. This is very useful, as the script itself isn't important to
the build system. As such, this line can be committed to the project and no
users will ever have to care about its presence.

### Enable Target Debugging

To use Target Debugging, the feature must first be enabled by setting the
``cmake.experimental.enableTargetDebugging`` to ``true`` in ``settings.json``
for Visual Studio Code. After this is done, the ``CMake: Configure`` command
must be run at least once to generate the new metadata.

### Selecting a Debug Target

Once the above steps have been completed and a CMake project is opened,
the following new entries should appear on the status bar:

![Target Debugging](../images/target_debugging_marked.png)

The left-hand button will launch the selected executable target in the debugger.
Initially, the text entry may read ``[No target selected for debugging]``. In
this case, the project may need to be configured again to generate the metadata.
Clicking this entry will launch a dialog to select a target.

### All Done!

That's it! CMake Tools uses metadata generated by CMake and the
``CMakeToolsHelpers`` script to automatically discover your executables and the
paths that they are written to. As executable targets are added to the project,
CMake Tools will discover them each time you re-configure the project. CMake
Tools will also rebuild the target associated with the executables when
that target is about to be debugged, so there is no need to worry about ensuring
the target is built before debugging.

## Configuring the debugger

CMake Tools generates a debugging configuration object on-the-fly, when
debugging is requested. To set some additional parameters to this debug
configuration, use the ``cmake.debugConfig``.