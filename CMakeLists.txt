cmake_minimum_required(VERSION 4.4)
project(codechecker LANGUAGES NONE)

find_package(Python3 3.9 EXACT REQUIRED COMPONENTS Interpreter)


if (NOT EXISTS ${venv_dir})
    execute_process(
        COMMAND ${Python3_EXECUTABLE} -m venv ${venv_dir}
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
        COMMAND_ERROR_IS_FATAL ANY
    )
    execute_process(
        COMMAND ${ACTIVATE_VENV}
    )
endif()
set(PYTHON_BIN ${venv_dir}/Scripts/python${CMAKE_EXECUTABLE_SUFFIX})
set(BUILD_DIR ${CMAKE_BINARY_DIR})
add_subdirectory(tools)
add_subdirectory(analyzer)

add_custom_target(package

    DEPENDS package_analyzer
)
