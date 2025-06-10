// README.md

This fork of [ros2/ros2](https://github.com/ros2/ros2) targets **MacOS 10.15+ on intel x86_64**, and is

Expanded from the official documentation available at
https://docs.ros.org/en/rolling/Installation/Alternatives/macOS-Development-Setup.html



## Key features, and with particular focus on:

- Addressing errors, including MacOS specific errors, encountered when building/debugging ros2_rolling

- OpenGL compatibility (RViz, OGRE, etc.)

- Builds with Apple SIP System Integrity Protection ENABLED

- Manual patching of vendor packages (eg., assimp, ogre) to ensure Darwin support

- Tested with all fixes applied while building/running on MacOS 12.7.6 Monterey

- +Universal binary compatibility (limited in scope currently and included only where feasible)

Note: ARM64/Apple Silicon has not been tested and is not officially supported in this fork.




// SPDX-License-Identifier: Apache-2.0 //
