set(CURRENT_DIR ${CMAKE_CURRENT_LIST_DIR})
set(ROOT        ${CURRENT_DIR}           )
set(BUILD_DIR   ${CURRENT_DIR}/build     )

set(MERGE_CLANG_EXTDEF_MAPS_DIR  ${BUILD_DIR}/merge_clang_extdef_mappings)

add_custom_target(build_merge_clang_extdef_mappings
    COMMAND ${CMAKE_COMMAND} -E make_directory ${BUILD_DIR}
  	COMMAND ${PYTHON_BIN} setup.py build --build-purelib ${MERGE_CLANG_EXTDEF_MAPS_DIR}
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
