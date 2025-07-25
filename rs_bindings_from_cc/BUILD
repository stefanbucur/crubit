"""Generates Rust bindings from C++ headers."""

load(
    "@rules_rust//rust:defs.bzl",
    "rust_library",
)
load(
    "//common:multiplatform_testing.bzl",
    "multiplatform_rust_test",
)
load(
    "//common:crubit_wrapper_macros_oss.bzl",
    "crubit_cc_test",
    "crubit_rust_binary",
    "crubit_rust_test",
)
load(
    "//rs_bindings_from_cc/bazel_support:deps_for_bindings.bzl",
    "deps_for_bindings",
)
load(
    "//rs_bindings_from_cc/bazel_support:rust_bindings_from_cc_binary.bzl",
    "rust_bindings_from_cc_binary",
)

package(
    default_applicable_licenses = ["//:license"],
    default_visibility = ["//:__subpackages__"],
)

rust_bindings_from_cc_binary(
    name = "rs_bindings_from_cc",
    binary = ":rs_bindings_from_cc_main",
    visibility = ["//:__subpackages__"],
)

deps_for_bindings(
    name = "deps_for_bindings",
    deps_for_generated_cc_file = [
        "//support/internal:bindings_support",
        "//support/public:bridge_cpp",
        "//support/public:status_bridge_cpp",
    ],
    deps_for_generated_rs_file = [
        "//support:ctor",
        "//support:forward_declare",
        "//support:oops",
        "//support/public:bridge_rust",
        # Required for `Copy` trait assertions added to the generated Rust
        # code.
        "@crate_index//:static_assertions",
        # Required for `cxx.rs` integration.
        "@crate_index//:cxx",
    ],
    visibility = ["//:__subpackages__"],
)

crubit_rust_binary(
    name = "rs_bindings_from_cc_main",
    srcs = ["rs_bindings_from_cc.rs"],
    # Do not add a fake dependency on Crubit, because it will introduce a
    # dependency cycle.
    crubit_dep = False,
    visibility = ["//visibility:public"],
    deps = [
        ":rs_bindings_from_cc_impl",
    ],
)

cc_library(
    name = "rs_bindings_from_cc_impl",
    srcs = ["rs_bindings_from_cc.cc"],
    deps = [
        ":cc_ir",
        ":cmdline",
        ":collect_namespaces",
        ":generate_bindings_and_metadata",
        "//common:file_io",
        "//common:status_macros",
        "@abseil-cpp//absl/flags:parse",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/strings:string_view",
        "@abseil-cpp//absl/types:span",
        "@llvm-project//llvm:Support",
    ],
)

cc_library(
    name = "generate_bindings_and_metadata",
    srcs = ["generate_bindings_and_metadata.cc"],
    hdrs = ["generate_bindings_and_metadata.h"],
    deps = [
        ":cc_collect_instantiations",
        ":cc_ir",
        ":cmdline",
        ":collect_namespaces",
        ":ir_from_cc",
        ":src_code_gen",
        "//common:status_macros",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings:string_view",
    ],
)

crubit_cc_test(
    name = "generate_bindings_and_metadata_test",
    srcs = ["generate_bindings_and_metadata_test.cc"],
    data = [
    ],
    tags = [
        # This test uses e.g. rustfmt and is difficult to run cross-platform. We could run it using
        # cross-compilation, but it's mostly uninteresting from a multi-platform POV.
        "not_run:arm",
        "not_run:mac",
    ],
    deps = [
        ":cc_ir",
        ":cmdline",
        ":collect_namespaces",
        ":generate_bindings_and_metadata",
        "//common:cc_ffi_types",
        "//common:status_macros",
        "//common:test_utils",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@googletest//:gtest_main",
    ],
)

cc_library(
    name = "ast_util",
    srcs = ["ast_util.cc"],
    hdrs = ["ast_util.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        "@abseil-cpp//absl/algorithm:container",
        "@abseil-cpp//absl/base:nullability",
        "@abseil-cpp//absl/functional:function_ref",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:basic",
    ],
)

cc_library(
    name = "bazel_types",
    srcs = [],
    hdrs = ["bazel_types.h"],
    deps = [
        "//common:string_type",
        "@abseil-cpp//absl/log:check",
    ],
)

cc_library(
    name = "type_map",
    srcs = ["type_map.cc"],
    hdrs = ["type_map.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        ":cc_ir",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:ast",
    ],
)

cc_library(
    name = "annotations_consumer",
    srcs = ["annotations_consumer.cc"],
    hdrs = ["annotations_consumer.h"],
    deps = [
        ":cc_ir",
        "//common:annotation_reader",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:ast",
    ],
)

cc_library(
    name = "cmdline",
    srcs = ["cmdline.cc"],
    hdrs = ["cmdline.h"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        ":cmdline_flags",
        "//common:cc_ffi_types",
        "//common:status_macros",
        "//common:string_view_conversion",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/debugging:leak_check",
        "@abseil-cpp//absl/flags:flag",
        "@abseil-cpp//absl/log",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//llvm:Support",
    ],
)

cc_library(
    name = "cmdline_flags",
    hdrs = ["cmdline_flags.h"],
    deps = ["@abseil-cpp//absl/flags:flag"],
)

crubit_cc_test(
    name = "cmdline_test",
    srcs = ["cmdline_test.cc"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        ":cmdline",
        ":cmdline_flags",
        "//common:cc_ffi_types",
        "//common:status_macros",
        "//common:status_test_matchers",
        "@abseil-cpp//absl/flags:flag",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:string_view",
        "@googletest//:gtest_main",
    ],
)

cc_library(
    name = "decl_importer",
    hdrs = ["decl_importer.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        "cc_ir",
        ":bazel_types",
        "//lifetime_annotations",
        "//lifetime_annotations:type_lifetimes",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/types:span",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:basic",
        "@llvm-project//clang:sema",
    ],
)

cc_library(
    name = "frontend_action",
    srcs = ["frontend_action.cc"],
    hdrs = ["frontend_action.h"],
    deps = [
        ":ast_consumer",
        ":decl_importer",
        "//lifetime_annotations",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:frontend",
        "@llvm-project//llvm:Support",
    ],
)

cc_library(
    name = "ast_consumer",
    srcs = ["ast_consumer.cc"],
    hdrs = ["ast_consumer.h"],
    deps = [
        ":decl_importer",
        ":importer",
        "@abseil-cpp//absl/log:check",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:frontend",
        "@llvm-project//clang:sema",
        "@llvm-project//llvm:Support",
    ],
)

cc_library(
    name = "importer",
    srcs = ["importer.cc"],
    hdrs = ["importer.h"],
    deps = [
        ":annotations_consumer",
        ":ast_util",
        ":bazel_types",
        ":cc_ir",
        ":decl_importer",
        ":recording_diagnostic_consumer",
        ":type_map",
        "//common:annotation_reader",
        "//common:status_macros",
        "//common:string_view_conversion",
        "//lifetime_annotations:type_lifetimes",
        "//rs_bindings_from_cc/importers:class_template",
        "//rs_bindings_from_cc/importers:cxx_record",
        "//rs_bindings_from_cc/importers:enum",
        "//rs_bindings_from_cc/importers:friend",
        "//rs_bindings_from_cc/importers:function",
        "//rs_bindings_from_cc/importers:function_template",
        "//rs_bindings_from_cc/importers:namespace",
        "//rs_bindings_from_cc/importers:type_alias",
        "//rs_bindings_from_cc/importers:type_map_override",
        "//rs_bindings_from_cc/importers:var",
        "@abseil-cpp//absl/base:no_destructor",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/log",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/log:die_if_null",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:cord",
        "@abseil-cpp//absl/strings:str_format",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:basic",
        "@llvm-project//clang:sema",
        "@llvm-project//llvm:Support",
    ],
)

crubit_cc_test(
    name = "importer_test",
    srcs = ["importer_test.cc"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        ":ir_from_cc",
        "//common:status_test_matchers",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/strings",
        "@googletest//:gtest_main",
    ],
)

cc_library(
    name = "cc_ir",
    srcs = ["ir.cc"],
    hdrs = ["ir.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        ":bazel_types",
        "//common:string_type",
        "//common:strong_int",
        "@abseil-cpp//absl/base:nullability",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:cord",
        "@abseil-cpp//absl/strings:str_format",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:ast",
        "@llvm-project//llvm:Support",
    ],
)

rust_library(
    name = "ir",
    srcs = ["ir.rs"],
    visibility = ["//:__subpackages__"],
    deps = [
        "//common:arc_anyhow",
        "//common:code_gen_utils",
        "//common:crubit_feature",
        "//common:error_report",
        "@crate_index//:flagset",
        "@crate_index//:itertools",
        "@crate_index//:proc-macro2",
        "@crate_index//:quote",
        "@crate_index//:serde",
        "@crate_index//:serde_json",
    ],
)

crubit_rust_test(
    name = "rs_ir_test",
    crate = ":ir",
    deps = [
        "@crate_index//:googletest",
    ],
)

rust_library(
    name = "ir_testing",
    testonly = 1,
    srcs = ["ir_testing.rs"],
    visibility = ["//rs_bindings_from_cc/generate_bindings:__subpackages__"],
    deps = [
        ":ir",
        ":json_from_cc",
        "//common:arc_anyhow",
        "//common:crubit_feature",
        "//common:ffi_types",
        "//common:multiplatform_testing",
        "@crate_index//:flagset",
        "@crate_index//:itertools",
    ],
)

crubit_rust_test(
    name = "ir_testing_test",
    crate = ":ir_testing",
    tags = [
        "not_run:arm",  # We don't need to run Crubit itself on aarch64.
        "not_run:mac",
    ],
    deps = [
        ":ir_matchers",
        ":ir_testing",
        "@crate_index//:googletest",
        "@crate_index//:quote",
    ],
)

cc_library(
    name = "ir_from_cc",
    srcs = ["ir_from_cc.cc"],
    hdrs = ["ir_from_cc.h"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        ":decl_importer",
        ":frontend_action",
        "//common:status_macros",
        "//common:string_view_conversion",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/container:flat_hash_set",
        "@abseil-cpp//absl/log:check",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:string_view",
        "@abseil-cpp//absl/types:span",
        "@llvm-project//clang:serialization",
        "@llvm-project//clang:tooling",
    ],
)

cc_library(
    name = "json_from_cc",
    testonly = 1,
    srcs = ["json_from_cc.cc"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        ":ir_from_cc",
        "//common:cc_ffi_types",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//llvm:Support",
    ],
)

multiplatform_rust_test(
    name = "ir_from_cc_test",
    timeout = "long",
    srcs = ["ir_from_cc_test.rs"],
    tags = [
        "not_run:arm",
        "not_run:mac",
    ],
    deps = [
        ":ir",
        ":ir_matchers",
        ":ir_testing",
        "//common:arc_anyhow",
        "//common:multiplatform_testing",
        "@crate_index//:googletest",
        "@crate_index//:itertools",
        "@crate_index//:proc-macro2",
        "@crate_index//:quote",
    ],
)

cc_library(
    name = "src_code_gen",
    srcs = ["src_code_gen.cc"],
    hdrs = ["src_code_gen.h"],
    deps = [
        ":cc_ir",
        "//common:cc_ffi_types",
        "//common:status_macros",
        "//rs_bindings_from_cc/generate_bindings:cc_api",  # buildcleaner: keep
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//llvm:Support",
    ],
)

cc_library(
    name = "ast_convert",
    srcs = ["ast_convert.cc"],
    hdrs = ["ast_convert.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        ":cc_ir",
        "@abseil-cpp//absl/functional:function_ref",
        "@abseil-cpp//absl/log:check",
        "@llvm-project//clang:ast",
        "@llvm-project//clang:basic",
    ],
)

cc_library(
    name = "recording_diagnostic_consumer",
    srcs = ["recording_diagnostic_consumer.cc"],
    hdrs = ["recording_diagnostic_consumer.h"],
    visibility = [
        "//:__subpackages__",
    ],
    deps = [
        "@abseil-cpp//absl/log",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/strings:str_format",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//clang:basic",
        "@llvm-project//llvm:Support",
    ],
)

rust_library(
    name = "ir_matchers",
    testonly = 1,
    srcs = ["ir_matchers.rs"],
    visibility = ["//:__subpackages__"],
    deps = [
        ":ir",
        ":ir_testing",
        "//common:token_stream_matchers",
        "//common:token_stream_printer",
        "@crate_index//:anyhow",
        "@crate_index//:itertools",
        "@crate_index//:proc-macro2",
        "@crate_index//:quote",
    ],
)

crubit_rust_test(
    name = "ir_matchers_test",
    crate = ":ir_matchers",
    tags = [
        "not_run:arm",  # We don't need to run Crubit itself on aarch64.
        "not_run:mac",
    ],
    deps = [
        "//common:arc_anyhow",
        "//common:multiplatform_testing",
        "@crate_index//:googletest",
    ],
)

filegroup(
    name = "builtin_headers",
    srcs = [
        "//llvm/llvm-project/clang:builtin_headers_filegroup",
        "//llvm/llvm-project/compiler-rt:fuzzer_filegroup",
        "//llvm/llvm-project/compiler-rt:sanitizer_filegroup",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "cc_collect_instantiations",
    srcs = ["collect_instantiations.cc"],
    hdrs = ["collect_instantiations.h"],
    deps = [
        ":collect_instantiations",  # build_cleaner: keep
        "//common:cc_ffi_types",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/types:span",
        "@llvm-project//llvm:Support",
    ],
)

crubit_cc_test(
    name = "cc_collect_instantiations_test",
    srcs = ["collect_instantiations_test.cc"],
    deps = [
        ":cc_collect_instantiations",
        "//common:status_test_matchers",
        "//common:test_utils",
        "@googletest//:gtest_main",
    ],
)

rust_library(
    name = "collect_instantiations",
    srcs = ["collect_instantiations.rs"],
    deps = [
        "//common:arc_anyhow",
        "//common:ffi_types",
        "@crate_index//:proc-macro2",
        "@crate_index//:serde_json",
        "@crate_index//:syn",
    ],
)

crubit_rust_test(
    name = "collect_instantiations_test",
    args = [
        # To prevent tests from polluting the environment variables for each other.
        "--test-threads",
        "1",
    ],
    crate = ":collect_instantiations",
    deps = [
        "@crate_index//:googletest",
        "@crate_index//:quote",
    ],
)

cc_library(
    name = "collect_namespaces",
    srcs = ["collect_namespaces.cc"],
    hdrs = ["collect_namespaces.h"],
    visibility = ["//:__subpackages__"],
    deps = [
        ":bazel_types",
        ":cc_ir",
        "@abseil-cpp//absl/container:btree",
        "@abseil-cpp//absl/container:flat_hash_map",
        "@abseil-cpp//absl/strings:string_view",
        "@llvm-project//llvm:Support",
    ],
)

crubit_cc_test(
    name = "collect_namespaces_test",
    srcs = ["collect_namespaces_test.cc"],
    deps = [
        ":cc_ir",
        ":collect_namespaces",
        ":ir_from_cc",
        "@abseil-cpp//absl/status",
        "@abseil-cpp//absl/status:statusor",
        "@abseil-cpp//absl/strings",
        "@abseil-cpp//absl/types:span",
        "@googletest//:gtest_main",
    ],
)
