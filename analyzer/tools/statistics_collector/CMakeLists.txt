set(CURRENT_DIR              ${CMAKE_CURRENT_LIST_DIR})
set(ROOT                     ${CURRENT_DIR})
set(BUILD_DIR                ${CURRENT_DIR}/build)
set(STATISTICS_COLLECTOR_DIR ${BUILD_DIR}/statistics_collector)
set(VENV_DEV_REQ_FILE        requirements_py/dev/requirements.txt)



add_custom_target(build_statistics_collector
    COMMAND ${CMAKE_COMMAND} -E make_directory ${BUILD_DIR}
    COMMAND ${PYTHON_BIN} setup.py build --build-purelib ${STATISTICS_COLLECTOR_DIR}
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
