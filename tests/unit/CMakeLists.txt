# transform defines to -D<definition> string
foreach(D IN LISTS DEFINES)
	SET(CMAKE_DEFINITIONS "${CMAKE_DEFINITIONS} -D${D}")
endforeach()

# Find GTEST and GMOCK frameworks
include(external/GoogleTestNMock)
ExternalProject_Get_Property(GoogleMock source_dir)
if(SYSTEM_INCLUDES)
	INCLUDE_DIRECTORIES(SYSTEM ${SYSTEM_INCLUDES})
endif()
include_directories(${source_dir}/include ${INCLUDES} ${source_dir}/gtest/include ${CMAKE_CURRENT_SOURCE_DIR})
LINK_DIRECTORIES(${THIRD_PARTY_DIR}/libs/gmock)

# Generate automatic unittest from jinja2 templates
ADD_CUSTOM_COMMAND(OUTPUT clone_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.jinja2
	clone_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating clone_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST clone_unittest.cc)

ADD_CUSTOM_COMMAND(OUTPUT DynamicObjectArray_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/base/DynamicObjectArray_unittest.cc.jinja2
	DynamicObjectArray_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/base/DynamicObjectArray_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating DynamicObjectArray_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST DynamicObjectArray_unittest.cc)

ADD_CUSTOM_COMMAND(OUTPUT SerializationAscii_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationAscii_unittest.cc.jinja2
	SerializationAscii_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationAscii_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating SerializationAscii_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST SerializationAscii_unittest.cc)

ADD_CUSTOM_COMMAND(OUTPUT SerializationHDF5_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationHDF5_unittest.cc.jinja2
	SerializationHDF5_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationHDF5_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating SerializationHDF5_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST SerializationHDF5_unittest.cc)

ADD_CUSTOM_COMMAND(OUTPUT SerializationJSON_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationJSON_unittest.cc.jinja2
	SerializationJSON_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationJSON_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating SerializationJSON_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST SerializationJSON_unittest.cc)

ADD_CUSTOM_COMMAND(OUTPUT SerializationXML_unittest.cc
	COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationXML_unittest.cc.jinja2
	SerializationXML_unittest.cc
	${LIBSHOGUN_SRC_DIR}/base/class_list.cpp
	DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/base/clone_unittest.cc.py
	${CMAKE_CURRENT_SOURCE_DIR}/io/SerializationXML_unittest.cc.jinja2
	WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
	COMMENT "Generating SerializationXML_unittest.cc")
LIST(APPEND TEMPLATE_GENERATED_UNITTEST SerializationXML_unittest.cc)

add_executable (discover_gtest_tests
       ${CMAKE_CURRENT_SOURCE_DIR}/discover_gtest_tests.cpp)

# collect the unittests
FILE(GLOB_RECURSE UNITTEST_SRC *_unittest.cc)

ADD_EXECUTABLE(shogun-unit-test ${UNITTEST_SRC} ${TEMPLATE_GENERATED_UNITTEST})
target_link_libraries(shogun-unit-test shogun gmock gtest ${SANITIZER_LIBRARY})
set_target_properties(shogun-unit-test PROPERTIES COMPILE_DEFINITIONS "${DEFINES}")
IF (SANITIZER_FLAGS)
	set_target_properties(shogun-unit-test PROPERTIES COMPILE_FLAGS ${SANITIZER_FLAGS})
ENDIF()

ADD_CUSTOM_TARGET(unit-tests
	COMMAND ${CMAKE_CURRENT_BINARY_DIR}/shogun-unit-test
	DEPENDS shogun-unit-test)

shogun_discover_tests(shogun-unit-test)

unset(CMAKE_DEFINITIONS)
