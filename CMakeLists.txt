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
set(CURRENT_DIR ${CMAKE_CURRENT_LIST_DIR})
set(PYTHON_BIN ${venv_dir}/Scripts/python${CMAKE_EXECUTABLE_SUFFIX})
set(BUILD_DIR ${CMAKE_BINARY_DIR})

set(CC_BUILD_DIR ${BUILD_DIR}/CodeChecker)

set(CC_BUILD_BIN_DIR ${CC_BUILD_DIR}/bin)
set(CC_BUILD_WEB_DIR ${CC_BUILD_DIR}/www)
set(CC_BUILD_LIB_DIR ${CC_BUILD_DIR}/lib/python3)
set(CC_WEB           ${CURRENT_DIR}/web)
set(CC_SERVER        ${CC_WEB}/server/)
set(CC_CLIENT        ${CC_WEB}/client/)
set(CC_ANALYZER      ${CURRENT_DIR}/analyzer)
set(CC_COMMON        ${CURRENT_DIR}/codechecker_common)

set(CC_TOOLS ${CURRENT_DIR}/tools)
set(CC_ANALYZER_TOOLS ${CC_ANALYZER}/tools)

# Root of the repository.
set(ROOT  ${CURRENT_DIR})

add_subdirectory(tools)
add_subdirectory(analyzer)

add_custom_target(package
# Copy libraries.
	COMMAND ${CMAKE_COMMAND} -E make_directory ${CC_BUILD_LIB_DIR}/codechecker
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${ROOT}/codechecker_common ${CC_BUILD_LIB_DIR}
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${CC_ANALYZER}/codechecker_analyzer ${CC_BUILD_LIB_DIR}/codechecker_analyzer

	# Copy config files and extend 'version.json' file with git information.
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${ROOT}/config ${CC_BUILD_DIR}/config
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${CC_ANALYZER}/config ${CC_BUILD_DIR}/config
	COMMAND ${PYTHON_BIN} ${CMAKE_CURRENT_LIST_DIR}/scripts/build/extend_version_file.py -r ${ROOT} ${CC_BUILD_DIR}/config/analyzer_version.json

	#	Copy CodeChecker binary.
	COMMAND ${CMAKE_COMMAND} -E copy ${ROOT}/bin/CodeChecker ${CC_BUILD_BIN_DIR}/CodeChecker

	# Allow lowercase codechecker command.
	COMMAND ${CMAKE_COMMAND} -E create_symlink ${CC_BUILD_BIN_DIR}/CodeChecker ${CC_BUILD_BIN_DIR}/codechecker

	# Copy license file.
	COMMAND ${CMAKE_COMMAND} -E copy ${ROOT}/LICENSE.TXT ${CC_BUILD_DIR}

    DEPENDS package_analyzer
)
