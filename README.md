# CMake Tutorial

- [Documentation](https://cmake.org/cmake/help/latest/index.html)
- [Guide](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)
- [Source code examples](https://github.com/Kitware/CMake/tree/master/Help/guide/tutorial)

## Basics

### `CMakeLists.txt`

- CMake input file.
- Multiple files can be used in a project: top-level and subdirectories.
- Define build process.
- Specify commands.

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
- Sets other variables too (e.g. `PROJECT_SOURCE_DIR`, `PROJECT_BINARY_DIR`).
- Required with every project.
- Should be called soon after `cmake_minimum_required()`.

#### [`add_executable(<name> <options>... <sources>...)`](https://cmake.org/cmake/help/latest/command/add_executable.html)

- Create an executable using specified source code files.
- `<name>` is the logical target name and must be globally unique within a project. Actual file name is constructed based on conventions of the native platform.
- Source files can be omitted if added later using `target_sources()`.

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
