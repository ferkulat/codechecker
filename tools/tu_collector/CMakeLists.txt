set(CURRENT_DIR      ${CMAKE_CURRENT_LIST_DIR})
set(BUILD_DIR        build)
set(BIN_DIR          ${BUILD_DIR}/bin)
set(TU_COLLECTOR_DIR ${BUILD_DIR}/tu_collector)

if(NOT PYTHON_BIN)
    set(PYTHON_BIN python3)
endif()

if(NOT ACTIVATE_DEV_VENV)
    set(ACTIVATE_DEV_VENV ./venv_dev/bin/activate)
endif()

if(NOT ACTIVATE_RUNTIME_VENV)
    set(ACTIVATE_RUNTIME_VENV ./venv/bin/activate)
endif()

if(NOT VENV_DEV_REQ_FILE)
    set(VENV_DEV_REQ_FILE requirements_py/dev/requirements.txt)
endif()




add_custom_target(codechecker_tools_tu_collector_build
    COMMAND ${PYTHON_BIN} setup.py build --build-purelib ${TU_COLLECTOR_DIR}
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
