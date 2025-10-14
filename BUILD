"""Targets in the repository root"""

# We prefer BUILD instead of BUILD.bazel
# gazelle:build_file_name BUILD

alias(
    name = "format",
    actual = "//tools/format",
)

exports_files(
    ["pyproject.toml"],
    visibility = ["//:__subpackages__"],
)

# Set `aspect configure` to produce aspect_rules_py targets rather than rules_python
# aspect:map_kind py_binary py_binary @aspect_rules_py//py:defs.bzl
# aspect:map_kind py_library py_library @aspect_rules_py//py:defs.bzl
# aspect:map_kind py_test py_test @aspect_rules_py//py:defs.bzl
#
# Don't walk into virtualenvs when looking for python sources.
# We don't intend to plant BUILD files there.
# aspect:exclude **/*.venv
