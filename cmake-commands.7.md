# cmake-commands(7)

> ### Content
> + [cmake-commands(7)](#cmake-commands7)
>   - [Scripting Commands](#scripting-commands)
>   - [Project Commands]()
>   - [CTest Commands]()
>   - [Deprecated Commands]()  

## Scripting Commands
这些命令总是可用的。

+ block
+ break
+ cmake_host_system_information
+ cmake_language
+ [cmake_minimum_required](command/cmake_minimum_required.md)
+ cmake_parse_arguments
+ cmake_path
+ cmake_policy
+ configure_file
+ continue
+ else
+ elseif
+ endblock
+ endforeach
+ endfunction
+ endif
+ endmacro
+ endwhile
+ execute_process
+ file
+ find_file
+ find_library
+ find_package
+ find_path
+ find_program
+ foreach
+ function
+ get_cmake_property
+ get_directory_property
+ get_filename_component
+ get_property
+ if
+ include
+ include_guard
+ list
+ macro
+ mark_as_advanced
+ math
+ message
+ option
+ return
+ separate_arguments
+ set
+ set_directory_properties
+ set_property
+ site_name
+ string
+ unset
+ variable_watch
+ while

## Project Commands

这些命令仅在CMake项目中可用。

+ add_compile_definitions
+ add_compile_options
+ add_custom_command
+ add_custom_target
+ add_definitions
+ add_dependencies
+ add_executable
+ add_library
+ add_link_options
+ add_subdirectory
+ add_test
+ aux_source_directory
+ build_command
+ cmake_file_api
+ create_test_sourcelist
+ define_property
+ enable_language
+ enable_testing
+ export
+ fltk_wrap_ui
+ get_source_file_property
+ get_target_property
+ get_test_property
+ include_directories
+ include_external_msproject
+ include_regular_expression
+ install
+ link_directories
+ link_libraries
+ load_cache
+ project
+ remove_definitions
+ set_source_files_properties
+ set_target_properties
+ set_tests_properties
+ source_group
+ target_compile_definitions
+ target_compile_features
+ target_compile_options
+ target_include_directories
+ target_link_directories
+ target_link_libraries
+ target_link_options
+ target_precompile_headers
+ target_sources
+ try_compile
+ try_run

## CTest Commands

这些命令仅在CTest脚本中可用。

+ ctest_build
+ ctest_configure
+ ctest_coverage
+ ctest_empty_binary_directory
+ ctest_memcheck
+ ctest_read_custom_files
+ ctest_run_script
+ ctest_sleep
+ ctest_start
+ ctest_submit
+ ctest_test
+ ctest_update
+ ctest_upload

## Deprecated Commands

这些命令已弃用，仅用于维护向后兼容性。每个命令的文档都说明了不赞成使用该命令的CMake版本。不要在新代码中使用这些命令。

+ build_name
+ exec_program
+ export_library_dependencies
+ install_files
+ install_programs
+ install_targets
+ load_command
+ make_directory
+ output_required_files
+ qt_wrap_cpp
+ qt_wrap_ui
+ remove
+ subdir_depends
+ subdirs
+ use_mangled_mesa
+ utility_source
+ variable_requires
+ write_file