find_package(Avro)

set(schemas_dir "${CMAKE_CURRENT_SOURCE_DIR}/avroschemas")

set(schemas
  manifest_file.schema.json
  manifest_entry.schema.json
)

set(avro_hdrs)
foreach (schema ${schemas})
  get_filename_component(struct_name ${schema} NAME_WE)
  set(avro_hdr "${CMAKE_CURRENT_BINARY_DIR}/${struct_name}.h")

  add_custom_command (
    OUTPUT ${avro_hdr}
    COMMAND avrogencpp
    -i ${schemas_dir}/${schema}
    -o ${avro_hdr} -n iceberg
    DEPENDS avrogencpp ${schemas_dir}/${schema}
    COMMENT "Running avrogencpp on ${schema}"
    VERBATIM)

  list(APPEND avro_hdrs ${avro_hdr})
endforeach()

v_cc_library(
  NAME iceberg
  SRCS ${avro_hdrs}
)

add_subdirectory(tests)
