cmake_minimum_required(VERSION 3.15) 

set(RTEMS_VERBOSE FALSE)

set(RTEMS_CONFIG_DIR 
	"${CMAKE_CURRENT_SOURCE_DIR}/../../rtems-cmake" 
	CACHE FILEPATH 
	"Directory containing the RTEMS *.cmake files"
)

################################################################################
# Load RTEMS pre-project configuration and checks
################################################################################
# Sanity checks
if(NOT RTEMS_PREFIX)
	if(NOT DEFINED ENV{RTEMS_BSP})
		message(FATAL_ERROR "No RTEMS prefix supplied!")
	else()
		set(RTEMS_PREFIX $ENV{RTEMS_BSP})
	endif()
endif()

if(NOT DEFINED RTEMS_BSP)
	if(NOT DEFINED ENV{RTEMS_BSP})
		message(FATAL_ERROR "No RTEMS BSP pair name supplied!")
	else()
		set(RTEMS_BSP $ENV{RTEMS_BSP})
	endif()
endif()

include(${RTEMS_CONFIG_DIR}/RTEMSPreProjectConfig.cmake)
rtems_pre_project_config(${RTEMS_PREFIX} ${RTEMS_BSP})

set(CMAKE_TOOLCHAIN_FILE ${RTEMS_CONFIG_DIR}/RTEMSToolchain.cmake) 

project(hello ASM C CXX)

set(TARGET_NAME ${CMAKE_PROJECT_NAME}) 

add_executable(${TARGET_NAME})

################################################################################
# Application
################################################################################

target_sources(${TARGET_NAME}
	PUBLIC
		init.c 
		hello.c
)

set(CMAKE_INSTALL_PREFIX "${CMAKE_CURRENT_BINARY_DIR}")
install(TARGETS ${CMAKE_PROJECT_NAME} DESTINATION "bin")

include("${RTEMS_CONFIG_DIR}/BuildType.cmake")
set_build_type()

add_custom_command(
	TARGET ${TARGET_NAME}
	POST_BUILD
	COMMAND echo "Generating binary file ${CMAKE_PROJECT_NAME}.bin"
	COMMAND ${CMAKE_OBJCOPY} -O binary ${TARGET_NAME} ${CMAKE_PROJECT_NAME}.bin
)

add_custom_command(
    TARGET ${TARGET_NAME}
    POST_BUILD
    COMMAND echo "RTEMS version: ${RTEMS_VERSION}"
    COMMAND echo "Built for RTEMS BSP: ${RTEMS_BSP}"
    COMMAND echo "Target build type: ${CMAKE_BUILD_TYPE}"
    COMMAND ${CMAKE_SIZE} ${TARGET_NAME}
)

################################################################################
# Load RTEMS post-project configuration and checks
################################################################################
include("${RTEMS_CONFIG_DIR}/RTEMSPostProjectConfig.cmake")
rtems_post_project_config(${TARGET_NAME})


