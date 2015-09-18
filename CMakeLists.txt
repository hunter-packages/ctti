project(ctti)
cmake_minimum_required(VERSION 3.3)

option(BUILD_TESTS "Build ctti feature tests" OFF)
option(BUILD_EXAMPLES "Build ctti examples" ON)

include(cmake/vs_source_groups.cmake)

function(ctti_target prefix name)

	# To see the headers in the solution...
	if(MSVC)
		generate_vs_source_groups(${prefix}s ${CMAKE_SOURCE_DIR}/${prefix}s ${prefix}_files)
		generate_vs_source_groups(include ${CMAKE_SOURCE_DIR}/include ctti_files)
	endif()

	add_executable(${prefix}_${name} ${CMAKE_SOURCE_DIR}/${prefix}s/${name}.cpp
	    ${${prefix}_files} ${ctti_files}
	)

	target_compile_options(${prefix}_${name} PRIVATE 
	    ${CTTI_GLOBAL_ALL_COMPILE_OPTIONS} 
	    ${CTTI_${prefix}_ALL_COMPILE_OPTIONS}
	    $<$<CONFIG:Debug>:${CTTI_GLOBAL_DEBUG_COMPILE_OPTIONS}>
	    $<$<CONFIG:Release>:${CTTI_GLOBAL_RELEASE_COMPILE_OPTIONS}> 
	    $<$<CONFIG:Debug>:${CTTI_${prefix}_DEBUG_COMPILE_OPTIONS}>
	    $<$<CONFIG:Release>:${CTTI_${prefix}_RELEASE_COMPILE_OPTIONS}>
	)

	target_include_directories(${prefix}_${name} PRIVATE 
	    ${CMAKE_SOURCE_DIR}/include/ # ctti headers
	    ${CMAKE_SOURCE_DIR}/${prefix}s/include/ # examples/tests extra includes
    )
endfunction()

function(ctti_example example_name)
	ctti_target(example "${example_name}")
endfunction()

function(ctti_test test_name)
	ctti_target(test "${example_name}")
endfunction()


if(NOT MSVC)
	set(CTTI_GLOBAL_ALL_COMPILE_OPTIONS -std=c++14)
endif()


if(BUILD_TESTS)
	add_subdirectory(tests)
endif()

if(BUILD_EXAMPLES)
	add_subdirectory(examples)
endif()
