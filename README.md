# CMake Tutorial

- [Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/index.html)
- [Source code examples](https://github.com/Kitware/CMake/tree/master/Help/guide/tutorial)

Other references

- [Documentation](https://cmake.org/cmake/help/latest/index.html)
- [Mastering CMake](https://cmake.org/cmake/help/book/mastering-cmake/index.html)

## [`CMakeLists.txt`](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Writing%20CMakeLists%20Files.html)

- CMake input file.
- Multiple files can be used in a project: top-level and subdirectories.
- Define build process.
- Specify commands.

## [`CMakeCache.txt`](https://cmake.org/cmake/help/book/mastering-cmake/chapter/CMake%20Cache.html)

- Cache file containing variables that are set as part of corresponding build (i.e. a project build system file generation).
- Includes everything found by system introspection, paths to installed software, and flags used for the build.
- Cmake contains several GUIs to allow editing it.

## Commands

### [`cmake_minimum_required(VERSION <min>[...<policy-max>])`](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html)

- Specify minimum CMake version and policy settings.
- Sets `CMAKE_MINIMUM_REQUIRED_VERSION` variable to `<min>`.
- Implicitly invokes `cmake_policy(VERSION <min>[...<max>])`, which sets `CMAKE_POLICY_VERSION_MINIMUM` variable.
- Must be the very first command in the file.

### [`project(<project-name> ...)`](https://cmake.org/cmake/help/latest/command/project.html)

- Set project name (and other project level info e.g. language, version number).
- Sets `PROJECT_NAME` variable.
- Sets `CMAKE_PROJECT_NAME` variable when called from top-level `CMakeLists.txt`.
- Sets other variables too (e.g. `PROJECT_SOURCE_DIR`, `PROJECT_BINARY_DIR`). Project `build directory` and project `binary directory` are considered same.
- Required with every project.
- Should be called soon after `cmake_minimum_required()`.
- Primarily intended for the top-level `CMakeLists.txt` file of a project. Not recommended to call in subdirectories for typical project structures (use `add_subdirectory()` - on top-level `CMakeLists.txt` file - and `add_executable()`,`add_library()`, `target_include_directories()` - within corresponding subdirectories `CmakeLists.txt` file - instead).

Use `project(<project-name> VERSION <mayor>[.<minor>[.<patch>[.<tweak>]]])` to set version number.
- CMake will define `<project-name>_VERSION = <mayor>[.<minor>[.<patch>[.<tweak>]]]`
- and `<project-name>_VERSION_MAJOR = <mayor>`, `<project-name>_VERSION_MINOR = <minor>` (and so on) variables behind the scenes.

TODO: Verify if this is correct.
Side note:
- Cmake `project` is the equivalent to VS Build Tools `solution`.
- Cmake `target` is the equivalent to VS Build Tools `project`.

### [`add_subdirectory(<source-dir> [<binary-dir>] ...)`](https://cmake.org/cmake/help/latest/command/add_subdirectory.html)

- Add a subdirectory to the build.
- `<source-dir>` directory in which source `CMakeLists.txt` and code files are located. Path may be absolute or relative to the current directory.
- `<binary-dir>` directory in which to place output files. Path may be absolute or relative to the current output directory. Use `<source-dir>` value (before expanding any relative path) if not specified.
- `CMakeLists.txt` file in specified `<source-dir>` will be processed immediately before continuing.

### [`add_executable(<name> <options>... <sources>...)`](https://cmake.org/cmake/help/latest/command/add_executable.html)

- Create an executable using specified source code files.
- `<name>` is the logical target name and must be globally unique within a project. Actual file name is constructed based on conventions of the native platform (e.g. `<name>.exe`).
- Source files can be omitted if added later using `target_sources()`.

### [`add_library(<name> [<type>] [EXCLUDE_FROM_ALL] <sources>...)`](https://cmake.org/cmake/help/latest/command/add_library.html)

- Create a library using specified source code files.
- `<name>` is the logical target name and must be globally unique within a project. Actual file name is constructed based on conventions of the native platform (e.g. `<name>.lib`).
- `<type>` define the type of library to be created.

Normal libraries:
  - [`STATIC`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#static-libraries) Static library. Archive of (linked) object files. Use when linking other targets.
  - [`SHARED`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#shared-libraries) Dynamic library. May be linked by other targets and loaded at runtime.
  - [`MODULE`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#module-libraries) Plugin. May not be linked by other targets, but may be dynamically loaded at runtime.

Other libraries:
  - [`OBJECT`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#object-libraries) Compile source files without archiving or linking their object files into a library. Other targets may reference the objects using the expression `$<TARGET_OBJECTS:obj-lib-name>` as source.
  - [`INTERFACE`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#interface-libraries) Specify usage requirements for dependents. Does not compile sources and does not produce a library artifact.
  - [`IMPORTED`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#imported-targets) 
  - [`ALIAS`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#alias-targets) 

### [`target_link_libraries(<target> ... <item>... ...)`](https://cmake.org/cmake/help/latest/command/target_link_libraries.html)

- Specify libraries or flags to use when linking a given target and/or its dependents.
- `<target>` must have been created by a command such as `add_executable()` or `add_library()`.
- It may still require to specify with `target_include_directories()` the source directory of being linked library header file (e.g. in `Step2` project, including `MathFunctions.h` header file source directory is required when linking `MathFunctions` library to `Tutorial` executable because the latter does not know where `MathFunctions.h` header file is; otherwise, including `mysqrt.h` header file source directory is not required when linking `SqrtLibrary` library to `MathFunctions` library because the latter does already know where `mysqrt.h` header file is). See `target usage requirements`.

Note: added as a dependency in target project build system files (VS Build Tools `Link\AdditionalDependencies`).

### [`target_sources(<target> ... <INTERFACE|PUBLIC|PRIVATE> [items1...] ...)`](https://cmake.org/cmake/help/latest/command/target_sources.html)

- Add sources to a target.
- `<target>` must have been created by a command such as `add_executable()` or `add_library()`.

### [`target_include_directories(<target> ... <INTERFACE|PUBLIC|PRIVATE> [items1...] ...)`](https://cmake.org/cmake/help/latest/command/target_include_directories.html)

- Specify where the executable target should look for include files.
- `<target>` must have been created by a command such as `add_executable()` or `add_library()`. `<target>` does not have to be defined in the same directory as the `target_link_libraries` call.
- `<item>` may be a library target name, a full path to a library file, a plain library name and so on.

Note: added as an include in target project build system files (VS Build Tools `AdditionalIncludeDirectories`).

### [`target_compile_definitions(<target> ... <INTERFACE|PUBLIC|PRIVATE> [items1...] ...)`](https://cmake.org/cmake/help/latest/command/target_compile_definitions.html)

- Add compile definitions (or symbols) to a target.
- `<target>` must have been created by a command such as `add_executable()` or `add_library()`.

### [`target_compile_options(<target> ... <INTERFACE|PUBLIC|PRIVATE> [items1...] ...)`](https://cmake.org/cmake/help/latest/command/target_compile_options.html)

- Add compiler-specific options: optimization levels (`-O3`), warning flags (`-Wall`, `-Wextra`), debug symbols (`-g`), other.

### [`target_compile_features(<target> <INTERFACE|PUBLIC|PRIVATE> <feature> ...)`](https://cmake.org/cmake/help/latest/command/target_compile_features.html)

- Add modern C++ compiler features: C++ standard (`cxx_std_17`), variadic templates (`cxx_variadic_templates`), lambda expressions (`cxx_lambdas`), other.
- CMake will automatically add necessary compiler flags to enable requested features if compiler supports them.

### [`configure_file(<input> <output> ...)`](https://cmake.org/cmake/help/latest/command/configure_file.html)

- Copy an `<input>` file to an `<output>` file while performing transformations of the input file content.
- `<input>` relative path is treated with respect to the value of `CMAKE_CURRENT_SOURCE_DIR`.
- `<output>` relative path is treated with respect to the value of `CMAKE_CURRENT_BINARY_DIR`.
- Variables referenced in the input file content as `@VAR@`, `${VAR}`, `$CACHE{VAR}`, `$ENV{VAR}` will be replaced (empty string if not defined).
- Conventional and recommended location for the output is within the `build/binary directory`.
- Use case: make variables defined in `CMakeLists.txt` file available in source code (e.g. generated cxx header file). Must be still specified with `target_include_directories()`.

### [`set(...)`](https://cmake.org/cmake/help/latest/command/set.html)

- Set a `<variable>` to a given `<value>`.
- CMake first searches for a normal variable when evaluating variable references, cached second.

Normal: `set(<variable> <value>... [PARENT_SCOPE])`
- Unset if no `<value>` is given (equivalent to `unset()`).
- Use empty string `<value>` to clear a `<variable>` leaving it defined.

Cache: `set(<variable> <value>... CACHE <type> <docstring> [FORCE])`
- Do not override by default (require `FORCE` keyword).
- `<type>` and `<docstring>` must be specified.

Environment: `set(ENV{<variable>} [<value>])`

### [`unset(...)`](https://cmake.org/cmake/help/latest/command/unset.html)

- Unset (i.e. remove) a `<variable>`.

Normal & cache: `unset(<variable> [CACHE | PARENT_SCOPE])`
Environment: `unset(ENV{<variable>})`

### [`option(<variable> "<help_text>" [value])`](https://cmake.org/cmake/help/latest/command/option.html)

- Provide a boolean option that the user can optionally select.
- Do nothing if `<variable>` already set (as either normal or cache variable).
- Default to boolean `OFF` if no initial `<value> provided.

### [`list(...)`](https://cmake.org/cmake/help/latest/command/list.html)

- Operate on semicolon-separated lists.
- Operations: reading, search, modification, ordering.

Modification:
- `list(APPEND <list> [<element>...])`: append elements to `<list>`.

Note: Create a list with `set()` command (e.g. call `set(<variable> Item1 Item2 ... ItemN)` to define `<variable>` with value `Item1;Item2;...;ItemN`).

### [`if(<condition>)`](https://cmake.org/cmake/help/latest/command/if.html)

- Execute a group of commands conditionally.
- Use along `elseif(<condition>)`, `else()`, `endif()`.

### [`install(...)`](https://cmake.org/cmake/help/latest/command/install.html)

- Specify rules to run at install time.
- Install rules are executed in order during installation. Install rules in subdirectories are interleaved with those in parent directory.
- Generates a `cmake_install.cmake` file inside each build/binary directory.

There are `options` common to multiple signatures:
- `DESTINATION <dir>`: directory to which a file will be installed.
- Other: `PERMISSIONS`, `CONFIGURATIONS`, `COMPONENT`, `EXCLUDE_FROM_ALL`, `OPTIONAL`.

Signatures:
- `install(TARGETS <target>... [...])`: install target output artifacts and associated files.
- `install({FILES | PROGRAMS} <file>... [...])`: install files or programs.
- Other

References:
- [Mastering CMake - Installing Files](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Install.html)

## Variables

- It is possible to create variables for a project using `set()`.
- There are some special variables automatically defined by CMake (those starting with `CMAKE_` - avoid this naming convention for custom variable names). May be set before calling `add_executable()` to take effect.
- Reference/Dereference a variable: `${FOO}`.

### [`CMAKE_CXX_STANDARD`](https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD.html)

- Default value for `CXX_STANDARD` target property if set when a target is created.

### [`CMAKE_CXX_STANDARD_REQUIRED`](https://cmake.org/cmake/help/latest/variable/CMAKE_CXX_STANDARD_REQUIRED.html)

- Default value for `CXX_STANDARD_REQUIRED` target property if set when a target is created.

### [`CMAKE_SOURCE_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_SOURCE_DIR.html)

- Top-level `CMakeLists.txt` file source directory - Warning! Actually the one specified when `cmake` is initially invoked.
- Remains constant through the entire execution.

### [`CMAKE_CURRENT_SOURCE_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_SOURCE_DIR.html)

- Currently being processed `CMakeLists.txt` file source directory.
- Same value as `CMAKE_SOURCE_DIR` for top-level `CMakeLists.txt` file.
- Value changes for each subdirectory `CMakeLists.txt` file (those added with `add_subdirectory()`).

### [`PROJECT_SOURCE_DIR`](https://cmake.org/cmake/help/latest/variable/PROJECT_SOURCE_DIR.html)

- Most recently defined project source directory in the current scope (i.e. keep parent project source directory while not processing any subproject - even while processing non-project subdirectory; take subproject source directory on processing and behave the same; restore parent project source directory when finish processing subproject).

Side note: a subproject is a subdirectory (`add_subdirectory()`) that calls `project()`.

### [`CMAKE_BINARY_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_BINARY_DIR.html)

- Top-level build directory (the one specified when `cmake` is initially invoked).
- Associated to top-level `CMakeLists.txt` file - Warning! Actually the one specified when `cmake` is initially invoked.

### [`CMAKE_CURRENT_BINARY_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_BINARY_DIR.html)

- Currently being processed `CMakeLists.txt` file binary directory.
- Same value as `CMAKE_BINARY_DIR` for top-level `CMakeLists.txt` file.
- Value changes for each subdirectory `CMakeLists.txt` file.

### [`PROJECT_BINARY_DIR`](https://cmake.org/cmake/help/latest/variable/PROJECT_BINARY_DIR.html)

- Most recently defined project binary directory in the current scope.

### [`CMAKE_INSTALL_PREFIX`](https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX.html)

- Install directory used by `install()`.
- Preprended onto all install directories (when using `cmake --install` or building `INSTALL`).

## Properties

Variables scoped to an object (e.g. a target).

### [`CXX_STANDARD`](https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html)

- The C++ standard whose features are requested to build a target.

### [`CXX_STANDARD_REQUIRED`](https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD_REQUIRED.html)

- Describe whether the value of `CXX_STANDARD` is a requirement or not.

## [CMake](https://cmake.org/cmake/help/latest/manual/cmake.1.html)

### [`Generate` a project build system files](https://cmake.org/cmake/help/latest/manual/cmake.1.html#generate-a-project-buildsystem)

Specifying source and build paths: `cmake -S <src-path> -B <build-path>`
- Path may be absolute or relative to the current working directory.
- Source tree must contain `CMakeLists.txt` file.

Using working directory as build tree and specifying source path:
- Create and navigate build directory: `mkdir <build-dir> && cd <build-dir>`
- Configure the project and generate native build system files: `cmake ../<src-dir>`

### [`Build` a project](https://cmake.org/cmake/help/latest/manual/cmake.1.html#build-a-project)

- Call the build system to compile/link the project (and others e.g. run test suite): `cmake --build <build-dir>` (or `cmake --build .` if already in the build directory).
- Acts as a wrapper around the underlying native build tool, abstracting away the specifics of the chosen build system.
- Re-runs build system file generation if needed (e.g. source files changed).

Side note:
- In-source build: place build artifacts within same directory as source code.
- Out-of-source build: keep build artifacts separate from source code. Maintain cleaner source tree and allow multiple independent builds from same source code.

### [`Install` a project](https://cmake.org/cmake/help/latest/manual/cmake.1.html#install-a-project)

- Install an already-built project: `cmake --install <build-dir> [<options>]`

Options:
- `--config <cfg>`: build configuration (useful for multi-generation generators).
- `--prefix <dir>`: override `CMAKE_INSTALL_PREFIX`.

Warning! Not recommended invoking `cmake_install.cmake` file with `cmake -P <script>`.

### [`Set/Override` an option](https://cmake.org/cmake/help/latest/manual/cmake.1.html#run-a-script)

- `cmake -D<var>=<value> <src-path>` (or `cmake -D<var>=<value> .` if already in the `CMakeLists.txt` file directory).
- Source tree must contain `CMakeLists.txt` file.
- `-D` set a `cache variable` (or override its value if already exists).
- Normal (non-cache) variable defined with `set()` without `CACHE` keyword cannot be overriden from command line.

## [`CTest`](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Testing%20With%20CMake%20and%20CTest.html)

Test an already-built project - Warning! Project must have enabled testing.

## [Target usage requirements](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-usage-requirements)

- Target settings that propagate to consumers/dependents (linked via `target_link_libraries()`) in order to correctly compile and link with it (i.e. `TargetB` target defines its own usage requirements which will be propagated to `TargetA` target when link with `target_link_libraries(TargetA ... TargetB)` - consumer target does not worry about any additional include or link directories).
- Represented by transitive compile and link properties.
- Modern CMake approach. Allow better control over a library/executable link and include line and over the transitive property of targets inside CMake.

Primary commands:
- `target_compile_definitions()`
- `target_compile_options()`
- `target_include_directories()`
- `target_link_directories()`
- `target_link_options()`
- `target_precompile_headers()`
- `target_sources()`

Keywords:
- INTERFACE: Apply only to consumer (dependent) targets.
- PUBLIC: Apply to both producer and consumer targets.
- PRIVATE: Apply only to producer target.

## [`Generator expressions`](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html)

- What? Expression evaluated during build system files generation.
- Why? Produce information specific to each [`build configuration`](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#build-configurations) (i.e. condition build system files generation).
- When? Allowed in the context of many target properties (e.g. `LINK_LIBRARIES`, `INCLUDE_DIRECTORIES`, `COMPILE_DEFINITIONS`) and commands to populate those (e.g. `target_link_libraries()`, `target_include_directories()`, `target_compile_definitions()`).

Main use case: enable conditional target usage requirements (e.g. linking, include directories, compile definitions/options/features) based on build configuration, platform information, target properties, other.

### Usage

- Syntax: `$<...>`.
- Allow nesting (e.g. `$<FOO:$<BAR:BAZ>>`).
- Surround it by qoutes when contains characters that may be interpreted as command argument separators (e.g. whitespaces, new lines, semicolons) to prevent it from being split.
- Use helper variables to build up a readable expression.

### Types

Conditional expressions:
- `$<condition:true_string>`: `true_string` if `1`; empty string if `0`; otherwise, error.
  - `$<0:true_string>`: force empty string.
  - `$<1:true_string>`: force `true_string`.
- `$<IF:condition,true_string,false_string>`: `true_string` if `1`; `false_string` if `0`; otherwise, error.
- `$<BOOL:string>`: convert (cast) `string` to `0` or `1` (no error).

Other: logical operators, primary comparison expressions, string transformations, list expressions, path expressions, configuration expressions, toolchain and language expressions, target-dependent expressions, export and install expressions, multi-level expression evaluation, escaped characters.

## Build process

Gathering and preparing the source code:
- Retrieve necessary source code files (may be located in a version control or a local directory).
- Preprocess the code (i.e. handle macros, conditional compilation, other language-specific features).

Compiling the code:
- Translate source code into machine-readable instructions (often in the form of object files).
- May involve resolving dependencies on external libraries.

Linking:
- Link compiled object files together with necessary libraries to create an executable or library.
- Resolve references between different parts of the code.

Packaging and artifact creation:
- Output a runable artifact.
- May involve creating archives, installers or other formats for deployment.

Testing and validation:
- Run automated test to verify the software functions correctly.
