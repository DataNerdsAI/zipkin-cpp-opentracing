find_path(OPENTRACING_INCLUDE_DIR NAMES opentracing/tracer.h)
find_library(OPENTRACING_LIB opentracing)

include_directories(SYSTEM ${OPENTRACING_INCLUDE_DIR})

set(EMBED_CONFIGURATION_SCHEMA_OUTPUT_FILE  
  ${CMAKE_CURRENT_BINARY_DIR}/tracer_configuration_schema.cpp)
add_custom_command(
  OUTPUT ${EMBED_CONFIGURATION_SCHEMA_OUTPUT_FILE}
  COMMAND embedfile tracer_configuration_schema ${CMAKE_CURRENT_SOURCE_DIR}/tracer_configuration.schema.json
  DEPENDS tracer_configuration.schema.json
)

install(DIRECTORY include/zipkin
        COMPONENT DEVEL
        DESTINATION include)
set(ZIPKIN_OPENTRACING_SRCS src/utility.cc
                            src/propagation.cc
                            src/dynamic_load.cc
                            src/tracer_factory.cc
                            src/opentracing.cc
                            src/sampling.cc
                            ${EMBED_CONFIGURATION_SCHEMA_OUTPUT_FILE})

           
add_library(zipkin_opentracing SHARED ${ZIPKIN_OPENTRACING_SRCS})
target_link_libraries(zipkin_opentracing ${OPENTRACING_LIB} zipkin)
set_target_properties(zipkin_opentracing PROPERTIES VERSION ${ZIPKIN_VERSION_STRING}
                                                    SOVERSION ${ZIPKIN_VERSION_MAJOR})
install(TARGETS zipkin_opentracing 
        COMPONENT DIST
        LIBRARY DESTINATION lib)

add_subdirectory(example)
add_subdirectory(test)
