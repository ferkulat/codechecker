set(CODEMIRROR https://cdnjs.cloudflare.com/ajax/libs/codemirror/5.30.0)

set(CURRENT_DIR          ${CMAKE_CURRENT_LIST_DIR})
set(ROOT                 ${CURRENT_DIR})
set(BUILD_DIR            ${CURRENT_DIR}/build)
set(REPORT_CONVERTER_DIR ${BUILD_DIR}/report_converter)
set(STATIC_DIR           ${CURRENT_DIR}/codechecker_report_converter/report/output/html/static)
set(VENDOR_DIR           ${STATIC_DIR}/vendor)
set(CODEMIRROR_DIR       ${VENDOR_DIR}/codemirror)

if(NOT VENV_DEV_REQ_FILE)
    set(VENV_DEV_REQ_FILE requirements_py/dev/requirements.txt)
endif()

add_custom_target(report_converter_dependecies
    COMMAND ${PYTHON_BIN} setup.py develop
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
add_custom_target(build_report_converter
    COMMAND ${CMAKE_COMMAND} -E make_directory ${BUILD_DIR}
    COMMAND ${PYTHON_BIN} setup.py build --build-purelib ${REPORT_CONVERTER_DIR}
    DEPENDS report_converter_dependecies
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)
