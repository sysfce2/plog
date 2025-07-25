if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    if(POLICY CMP0135) # Controls timestamps of the extracted contents (DOWNLOAD_EXTRACT_TIMESTAMP)
        cmake_policy(SET CMP0135 NEW)
    endif()

    # This sample requires GCC 6.0 or later
    if(NOT CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_VERSION VERSION_LESS 6.0)
        return()
    endif()

    include(FetchContent)

    # Select FreeRTOS port
    set(FREERTOS_PORT "GCC_POSIX")

    # Select FreeRTOS heap
    set(FREERTOS_HEAP "4")

    # Add the freertos_config for FreeRTOS-Kernel
    add_subdirectory(freertos_config)

    # Download FreeRTOS-Kernel sources
    FetchContent_Declare(
        freertos_kernel
        URL                        https://github.com/FreeRTOS/FreeRTOS-Kernel/archive/refs/tags/V11.1.0.tar.gz
        URL_HASH                   SHA256=0e21928b3bcc4f9bcaf7333fb1c8c0299d97e2ec9e13e3faa2c5a7ac8a3bc573
    )

    FetchContent_MakeAvailable(freertos_kernel)

    # Describe our executable
    add_executable(FreeRTOS main.cpp)
    target_link_libraries(FreeRTOS freertos_kernel freertos_config plog::plog)

    # Important!!! There is no standard way to know if the code is compiled for FreeRTOS. So we define __FREERTOS__ macro.
    target_compile_definitions(FreeRTOS PUBLIC __FREERTOS__)
endif()
