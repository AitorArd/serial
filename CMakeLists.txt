cmake_minimum_required(VERSION 2.8.3)
project(serial)

option(${PROJECT_NAME}_BUILD_SAMPLES        "Build serial samples"                         ON )

## Sources
set(serial_SRCS
    src/serial.cc
    include/serial/serial.h
    include/serial/v8stdint.h
)
if(APPLE)
	# If OSX
	list(APPEND serial_SRCS src/impl/unix.cc)
	list(APPEND serial_SRCS src/impl/list_ports/list_ports_osx.cc)
elseif(WIN32)
    # If windows
    list(APPEND serial_SRCS src/impl/win.cc)
    list(APPEND serial_SRCS src/impl/list_ports/list_ports_win.cc)
else()
    # If unix
    list(APPEND serial_SRCS src/impl/unix.cc)
    list(APPEND serial_SRCS src/impl/list_ports/list_ports_linux.cc)
endif()

## Add serial library
add_library(${PROJECT_NAME} ${serial_SRCS})
if(APPLE)
    target_link_libraries(${PROJECT_NAME} ${FOUNDATION_LIBRARY} ${IOKIT_LIBRARY})
elseif(WIN32)
    target_link_libraries(${PROJECT_NAME} setupapi)
else()
    target_link_libraries(${PROJECT_NAME} rt pthread)
endif()

## example
IF(${PROJECT_NAME}_BUILD_SAMPLES)
	add_executable(serial_example examples/serial_example.cc)
	add_dependencies(serial_example ${PROJECT_NAME})
	target_link_libraries(serial_example ${PROJECT_NAME})
ENDIF(${PROJECT_NAME}_BUILD_SAMPLES)

## Include headers
include_directories(include)
