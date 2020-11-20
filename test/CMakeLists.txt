

add_subdirectory("${PROJECT_SOURCE_DIR}/extern/googletest" "extern/googletest")

set(INSTALL_GTEST OFF)
set(BUILD_GMOCK OFF)

mark_as_advanced(
    # BUILD_GMOCK BUILD_GTEST BUILD_SHARED_LIBS
    BUILD_GTEST BUILD_SHARED_LIBS
    # gmock_build_tests gtest_build_samples gtest_build_tests
    gtest_disable_pthreads gtest_force_shared_crt gtest_hide_internal_symbols
)

set_target_properties(gtest PROPERTIES FOLDER extern)
set_target_properties(gtest_main PROPERTIES FOLDER extern)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g")

macro(package_add_test TESTNAME)
    add_executable(${TESTNAME} ${ARGN})
    
    # link the Google test infrastructure, mocking library, and a default main fuction to
    # the test executable.  Remove g_test_main if writing your own main function.
    target_link_libraries(${TESTNAME} PRIVATE project_options project_warnings
    gtest gtest_main
    
    )
    
    # gtest_discover_tests replaces gtest_add_tests,
    gtest_discover_tests(${TESTNAME}
        # set a working directory so your project root so that you can find test data via paths relative to the project root
        WORKING_DIRECTORY ${PROJECT_DIR}
        PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${PROJECT_DIR}"
    )
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()              

include_directories(${PROJECT_SOURCE_DIR}/src)

include_directories(${PROJECT_SOURCE_DIR}/extern/googletest/googletest/include)

package_add_test(tests tests.cc)
