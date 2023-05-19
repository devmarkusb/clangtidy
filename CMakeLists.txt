cmake_minimum_required(VERSION 3.24)

project(clangtidy)

set(MB_CLANGTIDY_VER 15 CACHE STRING
    "clang-tidy-? version to be used preferably, compatible with default .clang-tidy file")

find_program(CLANGTIDY_EXE NAMES clang-tidy-${MB_CLANGTIDY_VER} clang-tidy)
message("CLANGTIDY_EXE: ${CLANGTIDY_EXE}")

macro(glob_recurse_append_cxx_sources cxx_sources dir)
    file(GLOB_RECURSE
            s
            ${dir}/*.[chi]pp ${dir}/*.[chi] ${dir}/*.cu
            )
    list(APPEND ${cxx_sources} ${s})
endmacro()

# See README.md on usage.
macro(add_clang_tidy_project_target cxx_dirs)
    if (CLANGTIDY_EXE)
        unset(all_cxx_sources)
        foreach (cxx_dir ${cxx_dirs})
            glob_recurse_append_cxx_sources(all_cxx_sources ${cxx_dir})
        endforeach ()

        add_custom_target(
            clang-tidy-${PROJECT_NAME}
            COMMAND ${CLANGTIDY_EXE} --fix --fix-errors
            -p ${CMAKE_BINARY_DIR}/compile_commands.json
            ${all_cxx_sources}
        )
        unset(all_cxx_sources)
    endif ()
endmacro()
