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


## Setup

To prepare your environment for ROS 2 `vcs import` on macOS (Python 3.11), run the following:

```bash
git clone https://github.com/dirk-thomas/vcstool.git
cd vcstool
/Library/Frameworks/Python.framework/Versions/3.11/bin/python3 -m pip install -e .
```
Ensure the installed script is accessible via `PATH:`
```bash
which vcs
```
You should see something like:
```bash
/Library/Frameworks/Python.framework/Versions/3.11/bin/vcs
```
## Troubleshooting

### Python 3.11 SSL Certificate Error on macOS

If you encounter this error during `vcs import`:
<urlopen error [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1006)>


This is due to the Python 3.11 framework install not trusting system certificates by default. To fix this, run:

```bash
/Applications/Python\ 3.11/Install\ Certificates.command
```
### After ensuring you have python 3.11.9 from `python.org` and all dependencies* installed,
 you can run the following

```bash
cd ~/ros2_rolling
vcs import --input https://raw.githubusercontent.com/ros2/ros2/rolling/ros2.repos src
```
### Recovering from `vcs import` TLS or SSL errors

If `vcs import` fails to clone certain repositories due to TLS or certificate errors, 
then manually clone the affected modules into their correct paths within `~/ros2_rolling/src`, 
then rerun `vcs import` to validate all modules are in place.

```bash
cd ~/ros2_rolling/src

# 1. ament_lint
mkdir -p ament && cd ament
git clone https://github.com/ament/ament_lint.git
cd ..

# 2. Fast-CDR
mkdir -p eProsima && cd eProsima
git clone https://github.com/eProsima/Fast-CDR.git
cd ..

# 3. iceoryx
mkdir -p eclipse-iceoryx && cd eclipse-iceoryx
git clone https://github.com/eclipse-iceoryx/iceoryx.git
cd ..

# 4. rqt_console
mkdir -p ros-visualization && cd ros-visualization
git clone https://github.com/ros-visualization/rqt_console.git
cd ..

# 5. urdfdom_headers
mkdir -p ros && cd ros
git clone https://github.com/ros/urdfdom_headers.git
cd ..

# 6. message_filters
mkdir -p ros2 && cd ros2
git clone https://github.com/ros2/message_filters.git
cd ..

# 7. pybind11_vendor
git clone https://github.com/ros2/pybind11_vendor.git ros2/pybind11_vendor
```

## ⚠️ Note on Build Reproducibility

Building ros2_rolling on our 2014 Intel Mac mini (x86_64) was not trivial. 
Although we eventually achieved a fully complete, error-free build, without ommission of any part, 
retracing and documenting every step — including many required patches, 
module forks, and dependency workarounds — remains an ongoing effort...

As such, this repository is not yet complete, though it is actively maintained.

## We are incrementally:

Publishing forks of modules/submodules that required changes

Documenting all dependencies — whether installed via MacPorts, pip, or built from source (including transitive dependencies)

Preparing an updated ros2.repos file pointing to our corrected forks

Once complete, setup will be as simple as replacing the official ros2.repos file with our amended version, then performing a clean build.

## Progress:

If you see new commits, they likely reflect updates as we move toward that goal. 

Stay tuned — we’ll announce when the repository and all build instructions are finalized.





