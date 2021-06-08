# Minimum Working Example

This reproduces an error about a `go_test` which depends on
the external dependency [`com_github_grpc_ecosystem_grpc_gateway`](https://github.com/grpc-ecosystem/grpc-gateway).

There are two go targets in the `BUILD` file: `mwe` and `mwe_test`.
Both depend on `com_github_grpc_ecosystem_grpc_gateway`, and nothing else.
## `rules_proto` or `com_google_protobuf` loaded
When `rules_proto` or `com_google_protobuf` is loaded in the `WORKSPACE` file,
the `go_library` builds, but the `go_test` does not.

```
link: package conflict error: google.golang.org/genproto/protobuf/source_context: multiple copies of package passed to linker:
	@io_bazel_rules_go//proto/wkt:source_context_go_proto
	@org_golang_google_genproto//protobuf/source_context:source_context
Set "importmap" to different paths or use 'bazel cquery' to ensure only one
package with this path is linked.
Target //:mwe_test failed to build
```

This error occurs because `com_github_grpc_ecosystem_grpc_gateway` has its
own `BUILD` files, and there is a conflict about proto things.
So we generate our own.
```
    go_repository(
        name = "com_github_grpc_ecosystem_grpc_gateway",
        build_directives = [
            "gazelle:exclude **/**_test.go",
            "gazelle:exclude examples",
            "gazelle:resolve go github.com/grpc-ecosystem/grpc-gateway/internal //internal",
            "gazelle:resolve go github.com/grpc-ecosystem/grpc-gateway/protoc-gen-swagger/options //protoc-gen-swagger/options",
            ],
        build_file_generation = "on",
        build_file_name = "BUILD.bazel",
        build_file_proto_mode = "disable_global",
        importpath = "github.com/grpc-ecosystem/grpc-gateway",
        sum = "h1:gmcG1KaJ57LophUzW0Hy8NmPhnMZb4M0+kPpLofRdBo=",
        version = "v1.16.0",
    )
```

That doesn't explain why it fails for the test binary, but not the library.


## `rules_proto` and `com_google_protobuf` not loaded
When both `rules_proto` and `com_google_protobuf` are not loaded in
the `WORKSPACE` file, both the library
and the test fail, but with a different error.

```
ERROR: /home/tanya/.cache/bazel/_bazel_tanya/955d9fdfc7b7de1b487cf2acf1377142/external/io_bazel_rules_go/proto/BUILD.bazel:20:18: every rule of type _go_proto_compiler implicitly depends upon the target '@com_google_protobuf//:protoc', but this target could not be found because of: no such package '@com_google_protobuf//': The repository '@com_google_protobuf' could not be resolved
ERROR: Analysis of target '//:mwe' failed; build aborted: Analysis failed
```
