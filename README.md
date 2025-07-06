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


## Initial Setup

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
## After ensuring you have python 3.11.9 from `python.org` and all dependencies* installed,
 you can run the following

```bash
mkdir -p ~/ros2_rolling/src
cd ~/ros2_rolling
vcs import --input https://raw.githubusercontent.com/ros2/ros2/rolling/ros2.repos src
```
## Set up a virtual environment for ros2_rolling

```bash
python3.11 -m venv ~/ros2_venv
source ~/ros2_venv/bin/activate
```
Install core ROS 2 Python dependencies
```bash
pip install -U pip setuptools wheel
pip install colcon-common-extensions empy pybind11 PyYAML
```
Add MacOS GUI support for ROS 2 Python 
```bash
pip install \
  argcomplete importlib_metadata setuptools_scm \
  lxml ifcfg pytest pytest-cov mypy \
  catkin-pkg coverage matplotlib numpy \
  PyQt5 pycairo pytest-repeat pytest-rerunfailures 
```
And specifically to avoid `rosidl_generator_py` errors
```bash
pip install 'lark-parser==0.12.0'
```
## Installing PyGObject (via MacPorts)
ROS 2 packages requiring gi.repository (like GObject, Gtk, etc.) work best (we have found for mac x86_64) when using the MacPorts build of PyGObject rather than installing from PyPI.

1. Install PyGObject and GObject Introspection bindings:
```bash
sudo port install py311-gobject3 gobject-introspection
```
2. Bridge MacPorts Python into your venv:
If you're using a Python 3.11 virtual environment (e.g. `ros2_venv_dev`), add the following `.pth` file:
```bash
echo "/opt/local/Library/Frameworks/Python.framework/Versions/3.11/lib/python3.11/site-packages" > \
  ~/ros2_venv_dev/lib/python3.11/site-packages/macports.pth
```
This allows your venv to see MacPorts-installed Python packages.

3. Set required environment variables (at build/runtime):
```bash
export GI_TYPELIB_PATH=/opt/local/lib/girepository-1.0:$GI_TYPELIB_PATH
```
Optional: If needed by your build system or meson, also export:
```bash
export PKG_CONFIG_PATH=/opt/local/lib/pkgconfig:/opt/local/share/pkgconfig
export LD_LIBRARY_PATH=/opt/local/lib
```
4. Test:
```bash
python3 -c "from gi.repository import GObject; print(GObject)"
```
You should see a successful import and symbol print.

## Installing `setuptools==78.1.1` (via MacPorts)
We found that downgrading newer setuptools versions (especially ≥80.0)
to version 78.1.1 (via MacPorts) resolves ament_lint build errors, likely due to stricter PEP 517/660 python packaging standards in later versions.
```bash
sudo port install py311-setuptools @78.1.1_0
```
## Begin build
This will build up to `cyclonedds`...
```bash
colcon build --symlink-install
```
Due to incomplete install behavior of some Iceoryx components, a manual fix is required before building cyclonedds.
## Manually install libiceoryx_platform.dylib
```bash
mkdir -p ~/ros2_rolling/install/iceoryx_platform/lib

cp ~/ros2_rolling/build/iceoryx_hoofs/platform/libiceoryx_platform.dylib \
   ~/ros2_rolling/install/iceoryx_platform/lib/
```
Rebuild cyclonedds
```bash
colcon build --symlink-install --packages-select cyclonedds
```
This ensures all dependent libraries (e.g., `libiceoryx_binding_c`, `libiceoryx_posh`, `libiceoryx_hoofs`, `libiceoryx_platform`) resolve correctly without editing @rpath.

## ⚠️ Note on Build Reproducibility

Building ros2_rolling, inclusive of all it's 369 total parts on our 2014 Intel Mac mini (x86_64) was not trivial. 
Although we eventually achieved a fully complete, error-free build, without ommission of any part, 

our efforts retracing and documenting every step — including many required patches, 
module forks, and dependency workarounds, for the pupose of sharing our success with this repo — remain an ongoing effort...

As such, this repository is not yet complete, though it is actively maintained.

## We are incrementally:

Publishing forks of modules/submodules that required changes

Documenting all dependencies — whether installed via MacPorts, pip, or built from source (including transitive dependencies)

Preparing an updated ros2.repos file pointing to our corrected forks

Once complete, setup will be as simple as replacing the official ros2.repos file with our amended version, then performing a clean build.

## Progress:

If you see new commits, they likely reflect updates as we move toward that goal. 

Stay tuned — we’ll announce when the repository and all build instructions are finalized.





