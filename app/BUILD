load("@aspect_rules_py//py:defs.bzl", "py_binary", "py_library")

py_library(
    name = "foo_lib",
    srcs = ["foo_lib.py"],
)

py_binary(
    name = "app_bin",
    srcs = ["__main__.py"],
    main = "__main__.py",
    deps = [
        ":foo_lib",
    ],
)
