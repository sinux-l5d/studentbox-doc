---
title: "gRPC"
draft: false
tags: []
aliases:
- grpc
- gRPC
---

gRPC is "a high performance, open source universal RPC framework"[^grpcio].
RPC stands for *Remote Procedure Call*, which is a kind of API that abstract the complexity of making calls to a remote API by providing to the developer functions that works like if it was local[^wikipedia].

> [!quote] Quote
> 
> gRPC uses HTTP/2 under the covers, but HTTP is not exposed to the API designer[^googlecloud]

gRPC uses [[research/api/protobuf|Protocol Buffers]] as a serialisation format by default, but can use [[research/api/json|JSON]]. It works by extending the syntax of a `.proto` file.

We need to define the messages (basic Protobuf structure) that will be exchanged whenever it's a argument or a return value, along with the `services`, that describe the procedures available.

> [!example] Example from official tutorial[^grpcio]
>
> First the messages:
> ```protobuf
> // The request message containing the user's name.
> message HelloRequest {
>   string name = 1;
> }
> 
> // The response message containing the greetings
> message HelloReply {
>   string message = 1;
> }
> ```
>
> and then the gRPC syntax to declare a *service, a set of procedures*:
> ```protobuf
> // The greeter service definition.
> service Greeter {
>   // Sends a greeting
>   rpc SayHello (HelloRequest) returns (HelloReply) {}
> }
> ```

A few advantages of gRPC:
1. It abstracts away the calls to a remote API
2. Compatible with several popular languages
3. It makes possible to work with native objects of your current programming language
4. It generate the classes and object/messages for you from a `.proto` file

From the dedicated file, it creates a gRPC server to receive requests from clients, that themselves use a dynamic library generated via `protoc` and a dedicated plugin.

![Architecture of gRPC](research/api/grpc-architecture.svg)

This diagram illustrate how different clients/servers written in different languages can work together with the gRPC server and *stubs* (a client) created.

## File upload

Although a few users would recommend to use a [[research/api/rest|REST]] API to handle uploads, other says that it depends on the size of images and your app[^reddit_grpc_files].

Implementing file uploading in gRPC seems to be done via `stream`s[^betterprogramming][^vinsguru], one of [its features](https://grpc.io/docs/what-is-grpc/core-concepts/#client-streaming-rpc). The core principle is to send files in small chunk (by default, gRPC limits incoming messages to *4 MB* in case developer hasn't thought about messages size, but it can be increased[^sof_4mb]) Note that we send the metadata apart from the file, and it's not a standard so we have to define what's inside (path, name, extension/type...).

Implementations seems to also use `oneof` keyword to avoid sending metadata each time we're sending a chunk of file.
```protobuf
message MetaData {
	string filename = 1;
	string extension = 2;
}

message FileUploadRequest {
	oneof request {
		MetaData metadata = 1;
		bytes chunk = 2;
	}
}
```

[^grpcio]: _Introduction to grpc_ (2022) _gRPC_. Available at: https://grpc.io/docs/what-is-grpc/introduction/ (Accessed: January 14, 2023).
[^wikipedia]: _Remote procedure call_ (2023) _Wikipedia_. Wikimedia Foundation. Available at: https://en.wikipedia.org/wiki/Remote_procedure_call (Accessed: January 14, 2023).
[^googlecloud]: Nally, M. (2020) _GRPC vs rest: Understanding grpc, openapi and rest and when to use them in API design | google cloud blog_, _Google_. Google. Available at: https://cloud.google.com/blog/products/api-management/understanding-grpc-openapi-and-rest-and-when-to-use-them (Accessed: January 14, 2023).
[^reddit_grpc_files]: _r/grpc - grpc is suitable to handle file uploads?_ (no date) _reddit_. Available at: https://www.reddit.com/r/grpc/comments/oj2k9n/grpc_is_suitable_to_handle_file_uploads/ (Accessed: January 14, 2023).
[^betterprogramming]: Foong, N.W. (2022) _GRPC file upload and download in Python_, _Medium_. Better Programming. Available at: https://betterprogramming.pub/grpc-file-upload-and-download-in-python-910cc645bcf0 (Accessed: January 14, 2023).
[^vinsguru]: vIns (2022) _GRPC file upload with client streaming_, _Vinsguru_. Available at: https://www.vinsguru.com/grpc-file-upload-client-streaming/ (Accessed: January 14, 2023).
[^sof_4mb]: Anderson, E. (2019) _Should I transmit large data sets via grpc without manual chunking?_, _Stack Overflow_. Available at: https://stackoverflow.com/a/58435745 (Accessed: January 14, 2023).