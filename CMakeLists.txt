cmake_minimum_required(VERSION 3.13)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(jthread VERSION 22.07.0
        HOMEPAGE_URL https://github.com/StirlingLabs/jthread
        LANGUAGES CXX)

option(ENABLE_JTHREAD_TESTS "Enable jthread tests" ON)

add_library(jthread INTERFACE)

target_sources(jthread INTERFACE include/jthread include/stop_token)

target_include_directories(jthread
        INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

target_compile_features(jthread INTERFACE cxx_std_17)

include_directories(include)

include(GNUInstallDirs)

install(TARGETS jthread
        EXPORT jthread_Targets
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

install(DIRECTORY ${PROJECT_SOURCE_DIR}/include
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

if (ENABLE_JTHREAD_TESTS)
    enable_testing()

    add_executable(test_stoken
            include/stop_token
            source/condition_variable_any2.hpp
            source/test_stoken.cpp
            source/test.hpp)
    add_test(NAME test_stoken COMMAND test_stoken)
    set_tests_properties(test_stoken PROPERTIES TIMEOUT 30)

    add_executable(test_stokencb
            include/stop_token
            source/condition_variable_any2.hpp
            source/test_stokencb.cpp
            source/test.hpp)
    add_test(NAME test_stokencb COMMAND test_stokencb)
    set_tests_properties(test_stokencb PROPERTIES TIMEOUT 30)

    add_executable(test_stokenrace
            include/stop_token
            source/condition_variable_any2.hpp
            source/test_stokenrace.cpp
            source/test.hpp)
    add_test(NAME test_stokenrace COMMAND test_stokenrace)
    set_tests_properties(test_stokenrace PROPERTIES TIMEOUT 30)

    add_executable(test_stopcb
            include/stop_token
            source/test_stopcb.cpp
            source/test.hpp)
    add_test(NAME test_stopcb COMMAND test_stopcb)
    set_tests_properties(test_stopcb PROPERTIES TIMEOUT 30)

    add_executable(test_jthread1
            include/stop_token
            source/condition_variable_any2.hpp
            include/jthread
            source/test_jthread1.cpp
            source/test.hpp)
    add_test(NAME test_jthread1 COMMAND test_jthread1)
    set_tests_properties(test_jthread1 PROPERTIES TIMEOUT 30)

    add_executable(test_jthread2
            include/stop_token
            source/condition_variable_any2.hpp
            include/jthread
            source/test_jthread2.cpp
            source/test.hpp)
    add_test(NAME test_jthread2 COMMAND test_jthread2)
    set_tests_properties(test_jthread2 PROPERTIES TIMEOUT 30)

    # CV is just not ready for "prime time" (noop sched 1-core)
    if (NOT DEFINED ENV{CI})
        add_executable(test_cv
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cv.cpp
                source/test.hpp)
        add_test(NAME test_cv COMMAND test_cv)
        set_tests_properties(test_cv PROPERTIES TIMEOUT 30)

        add_executable(test_cvcb
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cvcb.cpp
                source/test.hpp)
        add_test(NAME test_cvcb COMMAND test_cvcb)
        set_tests_properties(test_cvcb PROPERTIES TIMEOUT 30)

        add_executable(test_cvrace
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cvrace.cpp
                source/test.hpp)
        add_test(NAME test_cvrace COMMAND test_cvrace)
        set_tests_properties(test_cvrace PROPERTIES TIMEOUT 30)

        add_executable(test_cvrace_hh
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cvrace_hh.cpp
                source/test.hpp)
        add_test(NAME test_cvrace_hh COMMAND test_cvrace_hh)
        set_tests_properties(test_cvrace_hh PROPERTIES TIMEOUT 30)

        add_executable(test_cvrace_stop
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cvrace_stop.cpp
                source/test.hpp)
        add_test(NAME test_cvrace_stop COMMAND test_cvrace_stop)
        set_tests_properties(test_cvrace_stop PROPERTIES TIMEOUT 30)

        add_executable(test_cvprodcons
                include/stop_token
                include/jthread
                source/condition_variable_any2.hpp
                source/test_cvprodcons.cpp
                source/test.hpp)
        add_test(NAME test_cvprodcons COMMAND test_cvprodcons)
        set_tests_properties(test_cvprodcons PROPERTIES TIMEOUT 30)
    endif ()

    # CV is just not ready for "prime time" (noop sched 1-core)
    if (NOT DEFINED ENV{CI})
        add_custom_target(build_tests)
        add_dependencies(build_tests
                test_stoken
                test_stokencb
                test_stokenrace
                test_stopcb
                test_jthread1
                test_jthread2
                test_cv
                test_cvcb
                test_cvrace
                test_cvrace_hh
                test_cvrace_stop
                test_cvprodcons)
    endif ()
endif ()
