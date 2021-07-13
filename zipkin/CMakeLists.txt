install(DIRECTORY include/zipkin
        COMPONENT DEVEL
        DESTINATION include)
set(ZIPKIN_SRCS src/zipkin_core_types.cc
                 src/utility.cc
                 src/hex.cc
                 src/tracer.cc
                 src/ip_address.cc
                 src/span_buffer.cc
                 src/span_context.cc
                 src/zipkin_reporter_impl.cc
                 src/zipkin_http_transporter.cc)

set(WIN32_LIBRARIES)
if(WIN32)
   list(APPEND WIN32_LIBRARIES wsock32.lib ws2_32.lib)
endif()
            
add_library(zipkin SHARED ${ZIPKIN_SRCS})
target_link_libraries(zipkin Threads::Threads ${CURL_LIBRARIES} ${WIN32_LIBRARIES})
set_target_properties(zipkin PROPERTIES VERSION ${ZIPKIN_VERSION_STRING}
                                  SOVERSION ${ZIPKIN_VERSION_MAJOR})
install(TARGETS zipkin 
  COMPONENT DIST
  LIBRARY DESTINATION lib)

add_subdirectory(test)
