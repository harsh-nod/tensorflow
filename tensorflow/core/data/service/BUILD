load("@com_github_grpc_grpc//bazel:cc_grpc_library.bzl", "cc_grpc_library")
load(
    "//tensorflow/core/platform:build_config.bzl",
    "tf_additional_all_protos",
    "tf_proto_library",
)
load(
    "//tensorflow:tensorflow.bzl",
    "tf_cc_test",
)

package(
    default_visibility = [
        "//tensorflow:internal",
    ],
    licenses = ["notice"],  # Apache 2.0
)

exports_files(["LICENSE"])

tf_proto_library(
    name = "common_proto",
    srcs = ["common.proto"],
    cc_api_version = 2,
    protodeps = tf_additional_all_protos(),
)

tf_proto_library(
    name = "master_proto",
    srcs = ["master.proto"],
    has_services = 1,
    cc_api_version = 2,
    protodeps = tf_additional_all_protos() + [
        ":common_proto",
    ],
)

tf_proto_library(
    name = "worker_proto",
    srcs = ["worker.proto"],
    has_services = 1,
    cc_api_version = 2,
    protodeps = tf_additional_all_protos() + [
        ":common_proto",
    ],
)

cc_library(
    name = "grpc_util",
    srcs = ["grpc_util.cc"],
    hdrs = [
        "grpc_util.h",
    ],
    deps = [
        "//tensorflow:grpc++",
        "//tensorflow/core:lib",
    ],
)

tf_cc_test(
    name = "grpc_util_test",
    srcs = ["grpc_util_test.cc"],
    deps = [
        ":grpc_util",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

cc_library(
    name = "compression_utils",
    srcs = ["compression_utils.cc"],
    hdrs = [
        "compression_utils.h",
    ],
    deps = [
        ":common_proto_cc",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:core_cpu_internal",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/profiler/lib:traceme",
        "@com_google_absl//absl/memory",
    ],
)

tf_cc_test(
    name = "compression_utils_test",
    srcs = ["compression_utils_test.cc"],
    deps = [
        ":compression_utils",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
        "//tensorflow/core/kernels/data:dataset_test_base",
    ],
)

cc_library(
    name = "credentials_factory",
    srcs = ["credentials_factory.cc"],
    hdrs = ["credentials_factory.h"],
    deps = [
        "//tensorflow:grpc++",
        "//tensorflow/core:lib",
        "@com_google_absl//absl/strings",
    ],
)

tf_cc_test(
    name = "credentials_factory_test",
    srcs = ["credentials_factory_test.cc"],
    deps = [
        ":credentials_factory",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

cc_library(
    name = "test_util",
    testonly = True,
    srcs = ["test_util.cc"],
    hdrs = [
        "test_util.h",
    ],
    data = glob(["testdata/*.pbtxt"]),
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core/framework:protos_all_cc",
        "//tensorflow/core/kernels/data:dataset_test_base",
    ],
)

tf_cc_test(
    name = "test_util_test",
    srcs = ["test_util_test.cc"],
    deps = [
        ":test_util",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
        "//tensorflow/core/data:standalone",
        "//tensorflow/core/kernels/data:dataset_test_base",
    ],
)

cc_grpc_library(
    name = "master_cc_grpc_proto",
    srcs = [":master_proto"],
    generate_mocks = True,
    grpc_only = True,
    deps = [":master_proto_cc"],
)

cc_grpc_library(
    name = "worker_cc_grpc_proto",
    srcs = [":worker_proto"],
    generate_mocks = True,
    grpc_only = True,
    deps = [":worker_proto_cc"],
)
