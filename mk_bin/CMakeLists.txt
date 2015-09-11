set(src
  monkey.c
  mk_signals.c
  )
if(CMAKE_SYSTEM_NAME STREQUAL "rtems")
set(src 
  rtems/rtems_network_config.c
  rtems/rtems_filesystem.c
  ${src})
endif()

add_executable(monkey-bin ${src})
target_link_libraries(monkey-bin monkey-core-static)

if(CMAKE_SYSTEM_NAME STREQUAL "rtems")
  target_link_libraries(monkey-bin libbsd.a)
endif()

set_target_properties(monkey-bin PROPERTIES OUTPUT_NAME monkey)

if(BUILD_LOCAL)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "../${CMAKE_CURRENT_BINARY_DIR}/")
else()
  install(TARGETS monkey-bin RUNTIME DESTINATION ${CMAKE_INSTALL_FULL_SBINDIR})
  if(SYSTEMD_DIR)
    configure_file (
      "${CMAKE_CURRENT_SOURCE_DIR}/systemd.in"
      "${CMAKE_CURRENT_BINARY_DIR}/monkey.service"
      )
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/monkey.service DESTINATION ${SYSTEMD_DIR})
  endif()
endif()

