load("//:third_party/clif.bzl", "pyclif_proto_library")

package(
    default_visibility = [
        "//visibility:public",
    ],
)

filegroup(
    name = "binaries",
    srcs = [
        "call_variants",
        "make_examples",
        "model_eval",
        "model_train",
        "postprocess_variants",
    ],
)

filegroup(
    name = "testdata",
    testonly = True,
    srcs = glob([
        "testdata/*.bam*",
        "testdata/*.bed*",
        "testdata/*.tfrecord*",
        "testdata/*.vcf*",
        "testdata/*.fasta*",
    ]),
)

test_suite(
    name = "gpu_tests",
    tags = [
        "gpu_test",
        "manual",
    ],
    tests = [
        ":call_variants_accelerator_test",
    ],
)

# Umbrella library for all the Python under DeepVariant.
py_library(
    name = "deepvariant_py",
    srcs = [],
    deps = [
        ":call_variants_lib",
        ":data_providers",
        ":make_examples_lib",
        ":model_eval_lib",
        ":model_train_lib",
        ":modeling",
        ":pileup_image",
        ":postprocess_variants_py_lib",
        ":tf_utils",
        "//deepvariant/python:allelecounter",
        "//deepvariant/python:variant_calling",
        "//deepvariant/realigner",
    ],
)

cc_library(
    name = "allelecounter",
    srcs = ["allelecounter.cc"],
    hdrs = ["allelecounter.h"],
    deps = [
        ":utils",
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core:reference",
        "//deepvariant/core/genomics:cigar_cc_pb2",
        "//deepvariant/core/genomics:position_cc_pb2",
        "//deepvariant/core/genomics:range_cc_pb2",
        "//deepvariant/core/genomics:reads_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_test(
    name = "allelecounter_test",
    size = "small",
    srcs = ["allelecounter_test.cc"],
    deps = [
        ":allelecounter",
        ":utils",
        "//deepvariant/core:cpp_test_utils",
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core:reference_fai",
        "//deepvariant/core:reference_test",
        "//deepvariant/core/genomics:position_cc_pb2",
        "//deepvariant/testing:gunit_extras",
        "@com_google_googletest//:gtest_main",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_library(
    name = "postprocess_variants_lib",
    srcs = ["postprocess_variants.cc"],
    hdrs = ["postprocess_variants.h"],
    deps = [
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "//deepvariant/core/protos:core_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "@org_tensorflow//tensorflow/core:lib",
        # redacted
        "@org_tensorflow//tensorflow/core/platform/cloud:gcs_file_system",
    ],
)

cc_test(
    name = "postprocess_variants_lib_test",
    size = "small",
    srcs = ["postprocess_variants_test.cc"],
    data = [
        ":testdata",
    ],
    deps = [
        ":postprocess_variants_lib",
        "//deepvariant/core:cpp_test_utils",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "@com_google_googletest//:gtest_main",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_library(
    name = "variant_calling",
    srcs = ["variant_calling.cc"],
    hdrs = ["variant_calling.h"],
    deps = [
        ":allelecounter",
        "//deepvariant/core:cpp_math",
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core:samplers",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "@org_tensorflow//tensorflow/core:lib",
        "@protobuf_archive//:protobuf",
    ],
)

cc_test(
    name = "variant_calling_test",
    size = "small",
    srcs = ["variant_calling_test.cc"],
    deps = [
        ":utils",
        ":variant_calling",
        "//deepvariant/core:cpp_test_utils",
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "//deepvariant/testing:gunit_extras",
        "@com_google_googletest//:gtest_main",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:test",
        "@protobuf_archive//:protobuf",
    ],
)

py_library(
    name = "variant_caller",
    srcs = ["variant_caller.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//deepvariant/core:py_math",
        "//deepvariant/core:variantutils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/python:variant_calling",
    ],
)

py_test(
    name = "variant_caller_test",
    size = "small",
    srcs = ["variant_caller_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":py_test_utils",
        ":variant_caller",
        "//deepvariant/core:variantutils",
        "//deepvariant/protos:deepvariant_py_pb2",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

cc_library(
    name = "utils",
    srcs = ["utils.cc"],
    hdrs = ["utils.h"],
    deps = [
        "//deepvariant/core:cpp_utils",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_test(
    name = "utils_test",
    size = "small",
    srcs = ["utils_test.cc"],
    deps = [
        ":utils",
        "//deepvariant/core:cpp_test_utils",
        "//deepvariant/testing:gunit_extras",
        "@com_google_googletest//:gtest_main",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

py_library(
    name = "make_examples_lib",
    srcs = ["make_examples.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":logging_level",
        ":pileup_image",
        ":tf_utils",
        ":variant_caller",
        ":variant_labeler",
        "//deepvariant/core:errors",
        "//deepvariant/core:genomics_io",
        "//deepvariant/core:htslib_gcp_oauth",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:proto_utils",
        "//deepvariant/core:py_utils",
        "//deepvariant/core:ranges",
        "//deepvariant/core:variantutils",
        "//deepvariant/core/protos:core_py_pb2",
        "//deepvariant/core/python:hts_verbose",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/python:allelecounter",
        "//deepvariant/realigner",
        "//deepvariant/vendor:timer",
        "@com_google_absl_py//absl/logging",
    ],
)

py_binary(
    name = "make_examples",
    srcs = [
        "make_examples.py",
    ],
    main = "make_examples.py",
    deps = [
        ":make_examples_lib",
    ],
)

py_test(
    name = "make_examples_test",
    size = "medium",
    srcs = ["make_examples_test.py"],
    data = [":testdata"],
    shard_count = 2,
    srcs_version = "PY2AND3",
    deps = [
        ":make_examples",
        ":py_test_utils",
        ":tf_utils",
        "//deepvariant/core:errors",
        "//deepvariant/core:genomics_io",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:ranges",
        "//deepvariant/core:variantutils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/core/protos:core_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/protos:realigner_py_pb2",
        "//deepvariant/testing:flagsaver",
        "@com_google_absl_py//absl/logging",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "call_variants_lib",
    srcs = ["call_variants.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":logging_level",
        ":modeling",
        ":tf_utils",
        "//deepvariant/core:errors",
        "//deepvariant/core:htslib_gcp_oauth",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:proto_utils",
        "//deepvariant/core:variantutils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
        "@com_google_absl_py//absl/logging",
    ],
)

py_binary(
    name = "call_variants",
    srcs = [
        "call_variants.py",
    ],
    main = "call_variants.py",
    deps = [
        ":call_variants_lib",
    ],
)

py_test(
    name = "call_variants_test",
    size = "medium",
    srcs = ["call_variants_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    deps = [
        ":call_variants",
        ":modeling",
        ":py_test_utils",
        ":tf_utils",
        "//deepvariant/core:errors",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:variantutils",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/testing:flagsaver",
        "@com_google_absl_py//absl/logging",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_test(
    name = "call_variants_accelerator_test",
    size = "large",
    srcs = ["call_variants_accelerator_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    tags = [
        "gpu_test",
        "manual",
        "notap",
    ],
    deps = [
        ":call_variants",
        ":modeling",
        ":py_test_utils",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "pileup_image",
    srcs = ["pileup_image.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//deepvariant/core:py_utils",
        "//deepvariant/core:ranges",
        "//deepvariant/core/protos:core_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/python:pileup_image_native",
    ],
)

py_test(
    name = "pileup_image_test",
    size = "small",
    srcs = ["pileup_image_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    deps = [
        ":pileup_image",
        ":py_test_utils",
        "//deepvariant/core:ranges",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/core/python:reference_fai",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/python:pileup_image_native",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

cc_library(
    name = "pileup_image_native",
    srcs = ["pileup_image_native.cc"],
    hdrs = ["pileup_image_native.h"],
    deps = [
        "//deepvariant/core/genomics:cigar_cc_pb2",
        "//deepvariant/core/genomics:position_cc_pb2",
        "//deepvariant/core/genomics:reads_cc_pb2",
        "//deepvariant/core/genomics:variants_cc_pb2",
        "//deepvariant/core/protos:core_cc_pb2",
        "//deepvariant/protos:deepvariant_cc_pb2",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

py_library(
    name = "postprocess_variants_py_lib",
    srcs = ["postprocess_variants.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":logging_level",
        "//deepvariant/core:errors",
        "//deepvariant/core:genomics_io",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:proto_utils",
        "//deepvariant/core:py_math",
        "//deepvariant/core:variantutils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/core/protos:core_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/python:postprocess_variants",
        "@com_google_absl_py//absl/logging",
    ],
)

py_binary(
    name = "postprocess_variants",
    srcs = [
        "postprocess_variants.py",
    ],
    main = "postprocess_variants.py",
    deps = [
        ":postprocess_variants_py_lib",
    ],
)

py_test(
    name = "postprocess_variants_test",
    size = "small",
    srcs = ["postprocess_variants_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    deps = [
        ":postprocess_variants",
        ":py_test_utils",
        "//deepvariant/core:errors",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:py_math",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
        "//deepvariant/testing:flagsaver",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "variant_labeler",
    srcs = ["variant_labeler.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//deepvariant/core:variantutils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "@com_google_absl_py//absl/logging",
    ],
)

py_test(
    name = "variant_labeler_test",
    size = "small",
    srcs = ["variant_labeler_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    deps = [
        ":py_test_utils",
        ":variant_labeler",
        "//deepvariant/core:ranges",
        "//deepvariant/core:variantutils",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "py_test_utils",
    testonly = True,
    srcs = ["test_utils.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//deepvariant/core:py_test_utils",
    ],
)

py_library(
    name = "data_providers",
    srcs = ["data_providers.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":tf_utils",
        "//deepvariant/core:io_utils",
        "//deepvariant/protos:deepvariant_py_pb2",
        "@protobuf_archive//:protobuf_python",
    ],
)

py_test(
    name = "data_providers_test",
    size = "small",
    srcs = ["data_providers_test.py"],
    data = [":testdata"],
    srcs_version = "PY2AND3",
    deps = [
        ":data_providers",
        ":modeling",
        ":py_test_utils",
        "//deepvariant/core:io_utils",
        "//deepvariant/core:variantutils",
        "//deepvariant/protos:deepvariant_py_pb2",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "modeling",
    srcs = ["modeling.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":tf_utils",
        "@com_google_absl_py//absl/logging",
        "@org_tensorflow_slim//:inception",
        "@org_tensorflow_slim//:mobilenet_v1",
        "@org_tensorflow_slim//:resnet_v2",
    ],
)

py_test(
    name = "modeling_test",
    size = "small",
    srcs = ["modeling_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":modeling",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "model_train_lib",
    srcs = ["model_train.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":data_providers",
        ":logging_level",
        ":modeling",
        "//deepvariant/core:proto_utils",
        "@com_google_absl_py//absl/logging",
    ],
)

py_binary(
    name = "model_train",
    srcs = ["model_train.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":model_train_lib",
    ],
)

py_test(
    name = "model_train_test",
    size = "medium",
    srcs = ["model_train_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":data_providers_test",
        ":model_train",
        ":modeling",
        ":py_test_utils",
        "//deepvariant/testing:flagsaver",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "model_eval_lib",
    srcs = ["model_eval.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":data_providers",
        ":logging_level",
        ":modeling",
        "//deepvariant/core:proto_utils",
        "//deepvariant/core:variantutils",
        "@com_google_absl_py//absl/logging",
    ],
)

py_binary(
    name = "model_eval",
    srcs = ["model_eval.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":model_eval_lib",
    ],
)

py_test(
    name = "model_eval_test",
    size = "medium",
    srcs = ["model_eval_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":data_providers_test",
        ":model_eval",
        ":modeling",
        ":py_test_utils",
        "//deepvariant/core:variantutils",
        "//deepvariant/testing:flagsaver",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "tf_utils",
    srcs = ["tf_utils.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//deepvariant/core:io_utils",
        "//deepvariant/core:ranges",
        "//deepvariant/core/genomics:variants_py_pb2",
        "//deepvariant/protos:deepvariant_py_pb2",
    ],
)

py_test(
    name = "tf_utils_test",
    size = "small",
    srcs = ["tf_utils_test.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":py_test_utils",
        ":tf_utils",
        "//deepvariant/core:io_utils",
        "//deepvariant/core/genomics:variants_py_pb2",
        "@com_google_absl_py//absl/testing:absltest",
        "@com_google_absl_py//absl/testing:parameterized",
    ],
)

py_library(
    name = "logging_level",
    srcs = ["logging_level.py"],
)
