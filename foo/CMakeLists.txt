add_library("${PROJECT_NAME}" "")
add_library(Foo::Foo ALIAS "${PROJECT_NAME}")

set(FOO_EXPORT_FILE "Foo-Exports")
install(TARGETS "${PROJECT_NAME}" EXPORT "${FOO_EXPORT_FILE}")

target_include_directories("${PROJECT_NAME}" PUBLIC
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>" #foo_export.h
	"$<INSTALL_INTERFACE:include>" #${CMAKE_INSTALL_PREFIX}/include
)

if(NOT BUILD_SHARED_LIBS)
	set(STATIC_POSTFIX "-s")
	set_target_properties("${PROJECT_NAME}" PROPERTIES 
	COMPILE_FLAGS -DFOO_STATIC_DEFINE)
endif()

#Rename target Foo
set_target_properties("${PROJECT_NAME}" PROPERTIES
	DEBUG_POSTFIX "${STATIC_POSTFIX}-d" 
	RELEASE_POSTFIX "${STATIC_POSTFIX}"
	MINSIZEREL_POSTFIX "${STATIC_POSTFIX}-mr"
	RELWITHDEBINFO_POSTFIX "${STATIC_POSTFIX}-rd"
)

include("GenerateExportHeader")
generate_export_header("${PROJECT_NAME}")
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/foo_export.h" DESTINATION "include")


add_subdirectory("include")
add_subdirectory("src")

set(FOO_CONFIG_DIR "lib/cmake/${PROJECT_NAME}")
configure_file("${Foo_SOURCE_DIR}/cmake/FooConfig.cmake.in" "${Foo_BINARY_DIR}/${FOO_CONFIG_DIR}/FooConfig.cmake")
install(FILES "${Foo_BINARY_DIR}/${FOO_CONFIG_DIR}/FooConfig.cmake" DESTINATION "${FOO_CONFIG_DIR}")
install(EXPORT "${FOO_EXPORT_FILE}" DESTINATION "${FOO_CONFIG_DIR}" NAMESPACE "Foo::")