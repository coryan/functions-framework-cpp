# ~~~
# Copyright 2020 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ~~~

if (BUILD_TESTING)
    find_package(GTest CONFIG REQUIRED)
    find_package(fmt CONFIG REQUIRED)
    find_package(google_cloud_cpp_storage REQUIRED)

    set(functions_framework_cpp_examples_unit_tests
        # cmake-format: sort
        storage_integration_test.cc storage_unit_test.cc)

    foreach (fname ${functions_framework_cpp_examples_unit_tests})
        string(REPLACE "/" "_" target "${fname}")
        string(REPLACE ".cc" "" target "${target}")
        add_executable("${target}" ${fname})
        functions_framework_cpp_add_common_options(${target})
        target_link_libraries(
            ${target}
            PRIVATE functions_framework_examples
                    functions-framework-cpp::framework
                    Boost::filesystem
                    Boost::log
                    GTest::gmock_main
                    GTest::gmock
                    GTest::gtest)
        add_test(NAME ${target} COMMAND ${target})
    endforeach ()

    add_executable(storage_integration_server storage_integration_server.cc)
    target_link_libraries(
        storage_integration_server
        PRIVATE functions_framework_examples functions-framework-cpp::framework
                Boost::filesystem Boost::log)
    add_executable(storage_system_test storage_system_test.cc)
    target_link_libraries(
        storage_system_test
        PRIVATE google-cloud-cpp::storage fmt::fmt Boost::log GTest::gmock_main
                GTest::gmock GTest::gtest)
    if (MSVC)
        set_property(
            SOURCE storage_system_test.cc
            APPEND
            PROPERTY COMPILE_DEFINITIONS _CRT_SECURE_NO_WARNINGS)
    endif ()
endif ()
