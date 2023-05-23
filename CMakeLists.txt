# Note, we intentionally don't use
#   set(CMAKE_CXX_CLANG_TIDY ${CLANGTIDY_EXE};)
# here, which would replace the build of any target by a clang-tidy run.
cmake_minimum_required(VERSION 3.24)

project(clangtidy)

set(MB_CLANGTIDY_VER 15 CACHE STRING
    "run-clang-tidy-? version to be used preferably, compatible with default .clang-tidy file")

find_program(RUN_CLANG_TIDY_EXE NAMES run-clang-tidy-${MB_CLANGTIDY_VER} run-clang-tidy)

message("RUN_CLANG_TIDY_EXE: ${RUN_CLANG_TIDY_EXE}")

macro(glob_recurse_append_cxx_sources cxx_sources dir)
    file(GLOB_RECURSE
            s
            ${dir}/*.[chi]pp ${dir}/*.[chi] ${dir}/*.cu
            )
    list(APPEND ${cxx_sources} ${s})
endmacro()

# See README.md on usage.
macro(add_clang_tidy_project_target cxx_dirs)
    if (RUN_CLANG_TIDY_EXE)
        unset(all_cxx_sources)
        foreach (cxx_dir ${cxx_dirs})
            glob_recurse_append_cxx_sources(all_cxx_sources ${cxx_dir})
        endforeach ()

        add_custom_target(
            clang-tidy-${PROJECT_NAME}
            COMMAND ${RUN_CLANG_TIDY_EXE} -fix
            -p ${CMAKE_BINARY_DIR}
            ${all_cxx_sources}
        )
        unset(all_cxx_sources)
    endif ()
endmacro()
