cmake_minimum_required(VERSION 3.10.0)

set(PATCH_VERSION "1" CACHE INTERNAL "Patch Version")
set(PROJECT_VERSION 0.0.${PATCH_VERSION})

project(async VERSION ${PROJECT_VERSION} LANGUAGES C CXX)

option(WITH_GTEST_TEST "Whether to build GTEST test" ON)

configure_file(version.h.in version.h)

add_executable(join_server
    src/main.cpp
    src/command_parser.cpp
    src/table.cpp
    src/database.cpp)

add_executable(unit_tests 
    tests/tests.cpp
    src/table.cpp
)

set_target_properties(join_server PROPERTIES
        CXX_STANDARD 17
        CXX_STANDARD_REQUIRED ON
        )

target_link_libraries(join_server
        ${Boost_LIBRARIES}
        ${CMAKE_THREAD_LIBS_INIT})

if(WITH_GTEST_TEST) 
    set_target_properties(unit_tests PROPERTIES
        CXX_STANDARD 17
        CXX_STANDARD_REQUIRED ON
        )
    target_link_libraries(unit_tests
        gtest
        pthread)
endif()

if (MSVC)
    target_compile_options(join_server PRIVATE
        /W4
    )
    if(WITH_GTEST_TEST) 
        target_compile_options(unit_tests PRIVATE
            /W4
        )
    endif()
else ()
    target_compile_options(join_server PRIVATE
        -Wall -Wextra -pedantic -Werror 
    )
    if(WITH_GTEST_TEST) 
        target_compile_options(unit_tests PRIVATE
            -Wall -Wextra -pedantic -Werror 
        )
    endif()
endif()

install(TARGETS join_server RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT example@example.com)

include(CPack)

if(WITH_GTEST_TEST)
    enable_testing()
    add_test(unit_tests unit_tests)
endif()


