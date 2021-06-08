workspace(name = "mwe-grpc_gateway-import")

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# rules_proto

# when this is missing, both the library and test fail with:
# ERROR: /home/tanya/.cache/bazel/_bazel_tanya/955d9fdfc7b7de1b487cf2acf1377142/external/io_bazel_rules_go/proto/BUILD.bazel:20:18: every rule of type _go_proto_compiler implicitly depends upon the target '@com_google_protobuf//:protoc', but this target could not be found because of: no such package '@com_google_protobuf//': The repository '@com_google_protobuf' could not be resolved

# when rules_proto is missing, the library builds, and the test fails with:
# ERROR: /home/tanya/Documents/tweagdocs/mwe-grpc_gateway-import/BUILD.bazel:15:8: GoLink mwe_test_/mwe_test failed: (Exit 1): builder failed: error executing command bazel-out/host/bin/external/go_sdk/builder link -sdk external/go_sdk -installsuffix linux_amd64 -arc ... (remaining 173 argument(s) skipped)

# Use --sandbox_debug to see verbose messages from the sandbox
# link: package conflict error: google.golang.org/genproto/protobuf/source_context: multiple copies of package passed to linker:
# @io_bazel_rules_go//proto/wkt:source_context_go_proto
# @org_golang_google_genproto//protobuf/source_context:source_context
# Set "importmap" to different paths or use 'bazel cquery' to ensure only one
# package with this path is linked.
# Target //:mwe_test failed to build
# Use --verbose_failures to see the command lines of failed build steps.
# INFO: Elapsed time: 507.860s, Critical Path: 71.01s
http_archive(
    name = "rules_proto",
    sha256 = "602e7161d9195e50246177e7c55b2f39950a9cf7366f74ed5f22fd45750cd208",
    strip_prefix = "rules_proto-97d8af4dc474595af3900dd85cb3a29ad28cc313",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_proto/archive/97d8af4dc474595af3900dd85cb3a29ad28cc313.tar.gz",
        "https://github.com/bazelbuild/rules_proto/archive/97d8af4dc474595af3900dd85cb3a29ad28cc313.tar.gz",
    ],
)

load("@rules_proto//proto:repositories.bzl", "rules_proto_dependencies", "rules_proto_toolchains")

rules_proto_dependencies()

rules_proto_toolchains()

# rules_go and gazelle
http_archive(
    name = "io_bazel_rules_go",
    sha256 = "69de5c704a05ff37862f7e0f5534d4f479418afc21806c887db544a316f3cb6b",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.27.0/rules_go-v0.27.0.tar.gz",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.27.0/rules_go-v0.27.0.tar.gz",
    ],
)

http_archive(
    name = "bazel_gazelle",
    sha256 = "62ca106be173579c0a167deb23358fdfe71ffa1e4cfdddf5582af26520f1c66f",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/bazel-gazelle/releases/download/v0.23.0/bazel-gazelle-v0.23.0.tar.gz",
        "https://github.com/bazelbuild/bazel-gazelle/releases/download/v0.23.0/bazel-gazelle-v0.23.0.tar.gz",
    ],
)

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")
load("@bazel_gazelle//:deps.bzl", "gazelle_dependencies", "go_repository")
load("//:deps.bzl", "go_dependencies")

# gazelle:repository_macro deps.bzl%go_dependencies
go_dependencies()

go_rules_dependencies()

go_register_toolchains(version = "1.16")

gazelle_dependencies()
