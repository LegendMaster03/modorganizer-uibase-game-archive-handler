# modorganizer-uibase

[![Build status](https://github.com/ModOrganizer2/modorganizer-uibase/actions/workflows/build.yml/badge.svg?branch=dev/vcpkg)](https://github.com/ModOrganizer2/modorganizer-uibase/actions)
[![Lint status](https://github.com/ModOrganizer2/modorganizer-uibase/actions/workflows/linting.yml/badge.svg?branch=dev/vcpkg)](https://github.com/ModOrganizer2/modorganizer-uibase/actions)

## GameArchiveHandler integration in this fork

This repository forks [ModOrganizer2/modorganizer-uibase](https://github.com/ModOrganizer2/modorganizer-uibase) to add `MOBase::GameArchiveHandler`, an optional game-feature interface for delegating archive extraction and creation to the active game plugin. The underlying UIBase library remains based on the upstream project; this fork does not implement a universal archive codec.

The contract and compatibility boundaries are described in [GameArchiveHandler integration](docs/game-archive-handler.md). Plugins consuming this feature must use a compatible UIBase build; the upstream build instructions below are retained.

## How to build?

```pwsh
# set to the appropriate path for Qt
$env:QT_ROOT = "C:\Qt\6.7.0\msvc2019_64"

# set to the appropriate path for VCPKG
$env:VCPKG_ROOT = "C:\vcpkg"

cmake --preset vs2022-windows "-DCMAKE_PREFIX_PATH=$env:QT_ROOT" `
    -DCMAKE_INSTALL_PREFIX=install `
    -DBUILD_TESTING=ON

# build uibase
cmake --build vsbuild --config RelWithDebInfo

# install uibase
cmake --install vsbuild --config RelWithDebInfo

# test uibase
ctest --test-dir vsbuild -C RelWithDebInfo --output-on-failure
```

Check [`CMakePresets.json`](CMakePresets.json) for some predefined values. Extra options
include:

- `BUILD_TESTING` - if specified, build tests for UIBase, requires the VCPKG `testing`
  feature to be enabled (enabled in the preset).

## How to use?

### As a VCPKG dependency

**Not implemented yet.**

### As a CMake target

Once the CMake targets for `uibase` are generated (see _How to build?_), you can include
`mo2::uibase` in your project:

1. Add `install/lib` to your `CMAKE_PREFIX_PATH` (replace `install` by the install
  location specified during build).
2. Use `uibase` in your `CMakeLists.txt`:

```cmake
find_package(mo2-uibase CONFIG REQUIRED)

add_library(myplugin SHARED)

target_link_libraries(myplugin PRIVATE mo2::uibase)
```
