cmake_minimum_required(VERSION 4.4)
project(analyzer LANGUAGES NONE)

set(VENV_DEV_REQ_FILE ${CMAKE_CURRENT_LIST_DIR}/requirements_py/dev/requirements.txt)

execute_process(
    COMMAND ${venv_dir}/Scripts/pip3${CMAKE_EXECUTABLE_SUFFIX} install -r ${VENV_DEV_REQ_FILE}
)

if(NOT BUILD_DIR)
  set(BUILD_DIR ${CMAKE_CURRENT_LIST_DIR}/build)
endif()

set(CC_BUILD_DIR     ${BUILD_DIR}/CodeChecker)
set(CC_BUILD_BIN_DIR ${CC_BUILD_DIR}/bin)
set(CC_BUILD_LIB_DIR ${CC_BUILD_DIR}/lib/python3)
set(ROOT             ${CMAKE_CURRENT_LIST_DIR}/..)
set(CC_TOOLS         ${ROOT}/tools)
set(CC_ANALYZER      ${ROOT}/analyzer)

add_subdirectory(tools)


add_custom_target(analyzer_package_dir_structure
    COMMAND ${CMAKE_COMMAND} -E make_directory ${BUILD_DIR}
    COMMAND ${CMAKE_COMMAND} -E make_directory ${CC_BUILD_BIN_DIR}
    COMMAND ${CMAKE_COMMAND} -E make_directory ${CC_BUILD_LIB_DIR}
)

add_custom_target(package_tu_collector
    # Copy tu_collector files.
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CC_TOOLS}/tu_collector/build/tu_collector/tu_collector ${CC_BUILD_LIB_DIR}/tu_collector
    COMMAND ${CMAKE_COMMAND} -D FILE_TO_SET_PERMISSION=${CC_BUILD_LIB_DIR}/tu_collector/tu_collector.py -P ${CMAKE_CURRENT_LIST_DIR}/set_file_permissions.cmake
    COMMAND ${CMAKE_COMMAND} -E create_symlink  ../lib/python3/tu_collector/tu_collector.py tu_collector
    DEPENDS codechecker_tools_tu_collector_build analyzer_package_dir_structure
    WORKING_DIRECTORY ${CC_BUILD_BIN_DIR}
)

add_custom_target(build_bazel_compile_commands
	DEPENDS tools_bazel_build
)


add_custom_target(package_bazel_compile_commands
	  # Copy bazel_compile_commands files.
	  COMMAND ${CMAKE_COMMAND} -E copy_directory ${CC_TOOLS}/bazel/build/bazel/bazel_compile_commands ${CC_BUILD_LIB_DIR}/bazel_compile_commands
	  COMMAND ${CMAKE_COMMAND} -D FILE_TO_SET_PERMISSION=${CC_BUILD_LIB_DIR}/bazel_compile_commands/bazel_compile_commands.py -P ${CMAKE_CURRENT_LIST_DIR}/set_file_permissions.cmake
	  COMMAND ${CMAKE_COMMAND} -E create_symlink ../lib/python3/bazel_compile_commands/bazel_compile_commands.py bazel-compile-commands
    DEPENDS build_bazel_compile_commands analyzer_package_dir_structure
    WORKING_DIRECTORY ${CC_BUILD_BIN_DIR}
)

add_custom_target(package_statistics_collector
    # Copy statistics-collector files.
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_LIST_DIR}/tools/statistics_collector/build/statistics_collector/codechecker_statistics_collector ${CC_BUILD_LIB_DIR}/codechecker_statistics_collector
    COMMAND ${CMAKE_COMMAND} -D FILE_TO_SET_PERMISSION=${CC_BUILD_LIB_DIR}/codechecker_statistics_collector/cli.py -P ${CMAKE_CURRENT_LIST_DIR}/set_file_permissions.cmake
    COMMAND ${CMAKE_COMMAND} -E create_symlink ../lib/python3/codechecker_statistics_collector/cli.py post-process-stats
    DEPENDS build_statistics_collector analyzer_package_dir_structure
    WORKING_DIRECTORY ${CC_BUILD_BIN_DIR}
)

add_custom_target(package_report_converter
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CC_TOOLS}/report-converter/build/report_converter/codechecker_report_converter ${CC_BUILD_LIB_DIR}/codechecker_report_converter
	  COMMAND ${CMAKE_COMMAND} -D FILE_TO_SET_PERMISSION=${CC_BUILD_LIB_DIR}/codechecker_report_converter/cli.py -P ${CMAKE_CURRENT_LIST_DIR}/set_file_permissions.cmake
	  COMMAND ${CMAKE_COMMAND} -E create_symlink ../lib/python3/codechecker_report_converter/cli.py report-converter
    WORKING_DIRECTORY ${CC_BUILD_BIN_DIR}
    DEPENDS build_report_converter analyzer_package_dir_structure
)
add_custom_target(package_merge_clang_extdef_mappings
    # Copy files.
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_LIST_DIR}/tools/merge_clang_extdef_mappings/build/merge_clang_extdef_mappings/codechecker_merge_clang_extdef_mappings ${CC_BUILD_LIB_DIR}/codechecker_merge_clang_extdef_mappings
    COMMAND ${CMAKE_COMMAND} -D FILE_TO_SET_PERMISSION=${CC_BUILD_LIB_DIR}/codechecker_merge_clang_extdef_mappings/cli.py -P ${CMAKE_CURRENT_LIST_DIR}/set_file_permissions.cmake
    COMMAND ${CMAKE_COMMAND} -E create_symlink  ../lib/python3/codechecker_merge_clang_extdef_mappings/cli.py merge-clang-extdef-mappings
    DEPENDS build_merge_clang_extdef_mappings analyzer_package_dir_structure
    WORKING_DIRECTORY ${CC_BUILD_BIN_DIR}
)

add_custom_target(package_analyzer
    DEPENDS analyzer_package_dir_structure package_tu_collector package_merge_clang_extdef_mappings package_report_converter package_statistics_collector package_bazel_compile_commands
)
