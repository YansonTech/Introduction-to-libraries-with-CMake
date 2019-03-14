add_executable("Example" "Main.cpp")
target_link_libraries("Example" PRIVATE Foo::Foo)