
find_package(GTest REQUIRED CONFIG)

set(SMSV_TEST_SOURCES
        correspondence
        bicubic_patch
        matrix_vector
        spherical_harmonics
        surface_deriv
        triangulator)

foreach(testname IN LISTS SMSV_TEST_SOURCES)
    set(test_target smvs_test_${testname})
    add_executable(${test_target} gtest_${testname}.cc)
    target_link_libraries(${test_target} GTest::gtest GTest::gtest_main smvs)

    add_test(NAME ${test_target} COMMAND ${test_target})
    unset(test_target)
endforeach()