# Minimum Working Example

This reproduces an error about a `go_test` which depends on
the external dependency [`com_github_grpc_ecosystem_grpc_gateway`](github.com/grpc-ecosystem/grpc-gateway).

There are two go targets in the `BUILD` file.
The `go_library` builds, but the `go_test` does not.
Both depend on `com_github_grpc_ecosystem_grpc_gateway`, and nothing else.

```
link: package conflict error: github.com/golang/protobuf/protoc-gen-go/descriptor: multiple copies of package passed to linker:
	@com_github_golang_protobuf//protoc-gen-go/descriptor:descriptor
	@io_bazel_rules_go//proto/wkt:descriptor_go_proto
Set "importmap" to different paths or use 'bazel cquery' to ensure only one
package with this path is linked.
Target //:fake_test failed to build
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

Interestingly, there is an error that only shows up for the 

https://github.com/bazelbuild/bazel/issues/11993
