
set(CURRENT_DIR          ${CMAKE_CURRENT_LIST_DIR})
set(ROOT                 ${CURRENT_DIR})
set(BUILD_DIR            ${CURRENT_DIR}/build)
set(COMPILE_COMMANDS_DIR ${BUILD_DIR}/bazel)

if(NOT PYTHON_BIN)
    set(PYTHON_BIN python3)
endif()

if (NOT ACTIVATE_DEV_VENV)
    set( ACTIVATE_DEV_VENV .venv_dev/bin/activate)
endif()

if (NOT ACTIVATE_RUNTIME_VENV)
    set(ACTIVATE_RUNTIME_VENV .venv/bin/activate)
endif()

if(NOT VENV_DEV_REQ_FILE)
    set(VENV_DEV_REQ_FILE requirements_py/dev/requirements.txt)
endif()

add_custom_target(tools_bazel_build
		COMMAND ${PYTHON_BIN} setup.py build --build-purelib ${COMPILE_COMMANDS_DIR}
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
