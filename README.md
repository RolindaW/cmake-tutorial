# CMake Tutorial

- [Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)
- [Source code examples](https://github.com/Kitware/CMake/tree/master/Help/guide/tutorial)

## Other references

- [Documentation](https://cmake.org/cmake/help/latest/index.html)
- [Mastering CMake](https://cmake.org/cmake/help/book/mastering-cmake/index.html)

## Basics

### [`CMakeLists.txt`](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Writing%20CMakeLists%20Files.html)

- CMake input file.
- Multiple files can be used in a project: top-level and subdirectories.
- Define build process.
- Specify commands.

### [`CMakeCache.txt`](https://cmake.org/cmake/help/book/mastering-cmake/chapter/CMake%20Cache.html)

- Cache file containing variables that are set as part of corresponding build (i.e. a project build system file generation).
- Includes everything found by system introspection, paths to installed software, and flags used for the build.
- Cmake contains several GUIs to allow editing it.

### Commands

#### [`cmake_minimum_required(VERSION <min>[...<policy_max_>])`](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html)

- Specify minimum CMake version and policy settings.
- Sets `CMAKE_MINIMUM_REQUIRED_VERSION` variable to `<min>`.
- Implicitly invokes `cmake_policy(VERSION <min>[...<max>])`, which sets `CMAKE_POLICY_VERSION_MINIMUM` variable.
- Must be the very first command in the file.

#### [`project(<project-name> ...)`](https://cmake.org/cmake/help/latest/command/project.html)

- Set project name (and other project level info e.g. language, version number).
- Sets `PROJECT_NAME` variable.
- Sets `CMAKE_PROJECT_NAME` variable when called from top-level `CMakeLists.txt`.
- Sets other variables too (e.g. `PROJECT_SOURCE_DIR`, `PROJECT_BINARY_DIR`). Project build directory and project binary directory are considered interchangable terms.
- Required with every project.
- Should be called soon after `cmake_minimum_required()`.

Use `project(<project-name> VERSION <mayor>[.<minor>[.<patch>[.<tweak>]]])` to set version number.
- CMake will define `<project-name>_VERSION = <mayor>[.<minor>[.<patch>[.<tweak>]]]`
- and `<project-name>_VERSION_MAJOR = <mayor>`, `<project-name>_VERSION_MINOR = <minor>` (and so on) variables behind the scenes.

#### [`add_executable(<name> <options>... <sources>...)`](https://cmake.org/cmake/help/latest/command/add_executable.html)

- Create an executable using specified source code files.
- `<name>` is the logical target name and must be globally unique within a project. Actual file name is constructed based on conventions of the native platform.
- Source files can be omitted if added later using `target_sources()`.

#### [`set(<variable> <value>... [PARENT_SCOPE])`](https://cmake.org/cmake/help/latest/command/set.html)

- Set/Unset (if no value is given) a variable.

#### [`unset(<variable> [CACHE | PARENT_SCOPE])`](https://cmake.org/cmake/help/latest/command/unset.html)

- Unset a variable.

#### [`configure_file(<input> <output> ...)`](https://cmake.org/cmake/help/latest/command/configure_file.html)

- Copy an `<input>` file to an `<output>` file while performing transformations of the input file content.
- `<input>` relative path is treated with respect to the value of `CMAKE_CURRENT_SOURCE_DIR`.
- `<output>` relative path is treated with respect to the value of `CMAKE_CURRENT_BINARY_DIR`.
- Variables referenced in the input file content as `@VAR@` (and other) will be replaced (empty string if not defined).
- Use case: make variables defined in `CMakeLists.txt` available in source code (e.g. generated HEADER file).

#### [`target_include_directories(<target> ... <INTERFACE|PUBLIC|PRIVATE> [items1...] ...)`](https://cmake.org/cmake/help/latest/command/target_include_directories.html)

- Specify where the executable target should look for include files.
- `<target>` must have been created by a command such as `add_executable()` or `add_library()`.

### Variables

- It is possible to create variables for a project using `set()`.
- There are some special variables automatically defined by CMake (those starting with `CMAKE_` - avoid this naming convention for custom variable names). May be set before calling `add_executable()` to take effect.

#### [`CMAKE_CXX_STANDARD`](https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html)

- Default value for `CXX_STANDARD` target property if set when a target is created.

#### [`CMAKE_CXX_STANDARD_REQUIRED`](https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD_REQUIRED.html)

- Default value for `CXX_STANDARD_REQUIRED` target property if set when a target is created.

### Properties

TODO

#### [`CXX_STANDARD`](https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html)

- The C++ standard whose features are requested to build a target.

#### [`CXX_STANDARD_REQUIRED`](https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD_REQUIRED.html)

- Describe whether the value of `CXX_STANDARD` is a requirement or not.

### [CMake](https://cmake.org/cmake/help/latest/manual/cmake.1.html)

#### Generate a project build system files

Specifying source and build paths: `cmake -S <src-path> -B <build-path>`
- Path may be absolute or relative to the current working directory.
- Source tree must contain `CMakeLists.txt` file.

Using working directory as build tree and specifying source path:
- Create and navigate build directory: `mkdir <build-dir> && cd <build-dir>`
- Configure the project and generate native build system files: `cmake ../<src-dir>`

#### Build a project

- Call the build system to compile/link the project (and others e.g. run test suite): `cmake --build <build-dir>` (or `cmake --build .` if already in the build directory).
- Acts as a wrapper around the underlying native build tool, abstracting away the specifics of the chosen build system.
- Re-runs build system file generation if needed (e.g. source files changed).
