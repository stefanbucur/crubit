load(
    "@bazel_skylib//rules:common_settings.bzl",
    "string_list_flag",
)
load("//cc_bindings_from_rs/bazel_support:toolchain.bzl", "cc_bindings_from_rs_toolchain")
load("//rs_bindings_from_cc/bazel_support:toolchain.bzl", "rs_bindings_from_cc_toolchain")

package(
    default_applicable_licenses = ["//:license"],
    default_visibility = ["//common/bazel_support:__subpackages__"],
)

# FLAGS
# =====
#
# The following flags can be used by either direction of interop to control behavior of the tool.

string_list_flag(
    name = "globally_enabled_features",
    build_setting_default = [],
    visibility = ["//visibility:public"],
)

# TOOLCHAINS
# ==========
#
# The following are all of the Crubit toolchain implementations.
# The actual registration happens in MODULE.bazel and toolchains/BUILD.

# cc_bindings_from_rs
# -------------------

cc_bindings_from_rs_toolchain(
    name = "cc_bindings_from_rs_on_demand_impl",
    binary = "//cc_bindings_from_rs:cc_bindings_from_rs",
)

# rs_bindings_from_cc
# -------------------

config_setting(
    name = "use_fake_rs_bindings_from_cc_setting",
    flag_values = {
        "//rs_bindings_from_cc/bazel_support:use_actual_bindings_generator": "False",
    },
)

config_setting(
    name = "use_actual_rs_bindings_from_cc_setting",
    flag_values = {
        "//rs_bindings_from_cc/bazel_support:use_actual_bindings_generator": "True",
    },
)

rs_bindings_from_cc_toolchain(
    name = "rs_bindings_from_cc_on_demand_impl",
    binary = "//rs_bindings_from_cc:rs_bindings_from_cc",
    builtin_headers = [
        "//llvm/llvm-project/clang:builtin_headers_filegroup",
        "//llvm/llvm-project/compiler-rt:fuzzer_filegroup",
        "//llvm/llvm-project/compiler-rt:sanitizer_filegroup",
    ],
    stl_headers = ["//nowhere/llvm:cc-std-headers"],
)

rs_bindings_from_cc_toolchain(
    name = "rs_bindings_from_cc_fake_impl",
    binary = "//rs_bindings_from_cc/bazel_support:fake_rust_bindings_from_cc",
    builtin_headers = [],
    stl_headers = [],
)

config_setting(
    name = "cpu_arm",
    constraint_values = [
        "@platforms//cpu:aarch64",
    ],
    visibility = [
    ],
)

config_setting(
    name = "cpu_x86",
    constraint_values = [
        "@platforms//cpu:x86_64",
    ],
    visibility = [
    ],
)
