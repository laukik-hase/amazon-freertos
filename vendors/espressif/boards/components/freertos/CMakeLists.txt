set(AMAZON_FREERTOS_KERNEL_DIR "${AFR_KERNEL_DIR}")
set(AMAZON_FREERTOS_FREERTOS_PLUS_DIR "${AFR_MODULES_FREERTOS_PLUS_DIR}")
set(AMAZON_FREERTOS_3RDPARTY_DIR "${AFR_3RDPARTY_DIR}")

string(FIND "${AFR_BOARD_NAME}" "esp32s3" SOC_NAME_ESP32S3)
string(FIND "${AFR_BOARD_NAME}" "esp32s2" SOC_NAME_ESP32S2)
string(FIND "${AFR_BOARD_NAME}" "esp32" SOC_NAME_ESP32)

if(NOT(${SOC_NAME_ESP32S3} EQUAL -1))
    set(SOC_NAME "esp32s3")
elseif(NOT(${SOC_NAME_ESP32S2} EQUAL -1))
    set(SOC_NAME "esp32s2")
elseif(NOT($SOC_NAME_ESP32) EQUAL -1)
    set(SOC_NAME "esp32")
endif()

set(
    include_dirs
    include
    ${AMAZON_FREERTOS_KERNEL_DIR}/include
    ${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/include
)

if(AFR_ENABLE_TESTS)
    list(APPEND include_dirs
        "${AMAZON_FREERTOS_FREERTOS_PLUS_DIR}/standard/freertos_plus_posix/include"
        "${AMAZON_FREERTOS_3RDPARTY_DIR}/unity/src"
        "${CMAKE_CURRENT_LIST_DIR}/../../${SOC_NAME}/aws_tests/config_files"
        )
else()
    list(APPEND include_dirs
        "${CMAKE_CURRENT_LIST_DIR}/../../${SOC_NAME}/aws_demos/config_files"
        )
endif()

# Edit following two lines to set component requirements (see docs)
set(COMPONENT_REQUIRES )
set(COMPONENT_PRIV_REQUIRES )

idf_component_register(SRCS "extra.c"
                            "task_snapshot.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/FreeRTOS-openocd.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/port.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/port_common.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/port_systick.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/portasm.S"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_context.S"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_init.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_loadstore_handler.S"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_overlay_os_hook.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_vector_defaults.S"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/portable/ThirdParty/GCC/Xtensa_ESP32/xtensa_vectors.S"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/croutine.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/event_groups.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/list.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/queue.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/stream_buffer.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/tasks.c"
                            "${AMAZON_FREERTOS_KERNEL_DIR}/timers.c"
                    INCLUDE_DIRS "${include_dirs}"
                    LDFRAGMENTS linker.lf
                    REQUIRES app_trace esp_timer
                    PRIV_REQUIRES soc)
