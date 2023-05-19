# clangtidy
Provides support for creating a CMake target per project that applies clang-tidy. Also serving as the single point of truth of at least a default .clang-tidy file.

## Requirements

Preferred clang-tidy version: see MB_CLANGTIDY_VER in CMakeLists.txt.
Although plain clang-tidy is used if the preferred version is not
available.

This needs to be updated when using features in `.clang-tidy`
not supported by lower versions.

## Usage

### Example cmake inclusion in your repo

```
cmake_minimum_required(VERSION 3.14)

include(FetchContent)

FetchContent_Declare(mb-clangtidy
        GIT_REPOSITORY "https://github.com/devmarkusb/clangtidy"
        GIT_TAG origin/HEAD
        GIT_SHALLOW ON
        )

FetchContent_MakeAvailable(mb-clangtidy)
```

In case you want to use the (up to date) default template .clang-tidy config file provided with this repo:
```
file(COPY ${mb-clangtidy_SOURCE_DIR}/.clang-tidy DESTINATION ${PROJECT_SOURCE_DIR}/)
```

Additionally, you have to include the following lines in your project's
`CMakeLists.txt` files, which will add a clang-tidy target per project.
When building the target every file from list of subdirs (recursively) will
be tidied.
```
set(cxx_dirs "apps;include;libs;sdks;source;src;test")

add_clang_tidy_project_target("${cxx_dirs}")
```
Be careful to really pass a semicolon separated list of subdir strings.
Also in the example `add_clang_tidy_project_target(${cxx_dirs})` without
quotes wouldn't work.
