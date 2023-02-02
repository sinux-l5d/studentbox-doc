# Project litterature review 

Student Name: Simon LEONARD
Student ID: D00262073

```toc
```

## Background

The research done in this paper helped me understand 3 types of APIs and how to achieve file transfer.

> [!important] About this paper
>
> This paper has been written with [Obsidian](https://obsidian.md) and is meant to be published as a website, available at https://sinux-l5d.github.io/studentbox-doc.

## Literature review

### JSON

> [!quote] From the official website
>
> **JSON** (JavaScript Object Notation) is a lightweight data-interchange format. It is easy for humans to read and write. It is easy for machines to parse and generate.
> 
> <small>_Introducing json_ (no date) _JSON_. Available at: https://www.json.org/json-en.html (Accessed: January 13, 2023).</small>

It's a serialisation format that is human-friendly, meaning objects, arrays and values are represented in a string. Example:

```json
{
    "name": "Simon",
    "surname": "Leonard",
    "age": 21,
    "hobbies": [
        "cooking",
        "reading",
        "drinking coffee"
    ],
    "enemies": null
}
```

Here is a speculative object that could represent me in JSON.

This time, let's say I have a array of users:

```json
{
    "users": [
        {
            "id": "12345",
            "name": "Simon",
            "surname": "Leonard",
            "age": 21,
            "hobbies": [
                "cooking",
                "reading",
                "drinking coffee"
            ],
            "allies": [
                "54321"
            ]
        },
        {
            "id": "54321",
            "name": "Lucas",
            "surname": "Thomas",
            "age": 22,
            "hobbies": [],
            "allies": [
                "12345"
            ]
        }
    ]
}
```

We can see that JSON has at least one drawback: the document's structure and the data are mixed. You can see above that we repeat the keys `id`, `name`, `surname`... for multiple users.

> [!question] Is it possible to transfer a file over JSON?
>
> Although protocols like REST and gRPC are more suitable for the task (with multipart upload and stream respectively), one could upload a file by encoding it in base64. Note that it increase the data size by ~33%[^33-percent]

### REST

REST (*Representational state transfer*) is a style of software architecture that takes advantage of HTTP(S):
- The path represent a resource
- A verb represent what kind of operation has to be done
- Headers are used for various things, like authorisation token
- The body for the actual data, when needed, nowadays mostly [[research/api/json|JSON]] for APIs
- The return status tells quickly if everything happened as expected

Let's say we have a database of recipes. We want to list them, add, modify and delete them. This can be done with a meaningful base path like `/recipes`. An API following REST principles would have those couples verbs/paths:
- `GET /recipes` to have a list of them (complete or partial objects, maybe with pagination) 
- `POST /recipes` to add one
- `PUT /recipes/:id` or `PATCH /recipes/:id` to modify a recipe fully or partially (where id is a unique identifier of the resource)
- `DELETE /recipes/:id` to delete it

#### File upload

For uploading files, we have several options. One is to use base64 encoding and put the result in JSON, but it's not the best option for large files. Although we might never have big files (because we exchange coding files), it is something to consider for assets for example.

Another way of doing this is through a `multipart/form-data` request[^multipart]. The principle is to first upload the files independently from their metadata or any data you could send. Either first upload the files, get IDs and link them to the metadata (server controls names/ids) or upload metadata first and then files (client controls names/ids).

### GraphQL

> [!quote] According to the official website
>
> GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools.
>
> <small>_GraphQL | A query language for you API_ (no date) _GraphQL_. Available at: https://graphql.org/ (Accessed: January 13, 2023).</small>

First thing we notice is that GraphQL is not just a API specifications but a full runtime[^aimuliple] unlike [[research/api/rest|REST]] which is just a specification.

For GraphQL, just as for [[research/api/grpc|gRPC]], we need to write specification of the objects we will transfer beforehand. In the GraphQL word, it's called *type definitions* and types contains *fields*.

> [!example] Example from the official website
>
> First describe the objects
> ```graphql
> type Project {
>   name: String
>   tagline: String
>   contributors: [User]
> }
> ```
>
> Then query whatever you want
> ```graphql
> {
>   project(name: "GraphQL") {
>     tagline
>   }
> }
> ```
>
> The result will be formatted just like the query
> ```graphql
> {
>   "project": {
>     "tagline": "A query language for APIs"
>   }
> }
> ```

Those type definition being in separate files, it's easy to document the API automatically with tools like [Graph*i*QL](https://github.com/graphql/graphiql).

One of the advantages of this query approach is its precision, allowing us to have all the data we need at once, without the need to fetch multiple times like it's the case for REST.

> [!danger] Drawbacks
>
> From https://research.aimultiple.com/graphql-vs-rest/
> 1. GraphQL's single endpoint (`/graphql`) can be a bottleneck, especially because REST allows easy caching on routes.
> 2. Security is not built in the standard, different alternatives exist.
> 3. "While GraphQL offers a caching mechanism, the cache-implementing process is much more complex and time-consuming than REST implementation." It's mostly because REST allows caching by using different URLs.
> 4. GraphQL costs more because queries can be unpredictably large, and a query's cost can be hard to estimate. 

#### File upload

Just like for REST, we could encode files in base64 to upload them. But that adds overhead for both server and client, increasing the file size by ~33%[^wundergraph].

Just like for REST, we could use a multipart request but:

> [!quote] Quote from Jens Neuse[^wundergraph]
>
>  Unfortunately, there's no standard to handle Multipart Requests with GraphQL. This means, your solution will not be easily portable across different languages or implementations and your client implementation depends on the exact implementation of the server.

So one of the other approaches is to have another homemade API in REST that takes care of files only, or using AWS S3. The last option is not viable if we want to keep our system independent from the cloud.

The article I relied on is made by Jens Neuse at [WunderGraph](https://wundergraph.com), described as "The simplicity of RPC with the power of GraphQL" on their website. That could be a solution, but would lock us in a not-so-well-known solution, and we don't control the prices. If I were to use RPC, I would probably go with [[research/api/grpc|gRPC]] which is free, open source and licensed Apache-2.0.

### Protocol Buffers

[Protobuf or Protocol Buffers](https://developers.google.com/protocol-buffers) is a binary serialisation format that needs type definition beforehand. It's created by Google. This allows to omit structure from the data serialised, as client and server know what it is about.

> [!quote]
>
> Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serialising structured data.
> <small>_Protocol buffers | google developers_ (no date) _Google_. Google. Available at: https://developers.google.com/protocol-buffers (Accessed: January 13, 2023).</small>

To demonstrate how it's working, I've recreated the same structure as in the [[research/api/json|JSON]] page.

A *type* in protobuf is called a `message`. Messages are defined in a `.proto` file. Let's create a list of people, and see how it's serialised compared to JSON.

```protobuf
syntax = "proto3";
package file;

message Person {
    string id = 1;
    string name = 2;
    string surname = 3;
    uint32 age = 4;
    repeated string hobbies = 5;
    repeated string allies = 6;
}

message PersonList {
    repeated Person users = 1;
}
```

Before going further, each property has an identifier (the numbers). They are used in serialisation.

I made a little experiment in JavaScript using [protobufjs](https://www.npmjs.com/package/protobufjs) to compare the length of messages in JSON (string) and Protobuf (binary):

```js
const protobuf = require("protobufjs");

const simon = {
  id: "12345",
  name: "Simon",
  surname: "Leonard",
  age: 21,
  hobbies: ["coding", "reading", "writing"],
  allies: [
    "54321",
  ],
};

const lucas = {
  id: "54321",
  name: "Lucas",
  surname: "Thomas",
  age: 22,
  hobbies: [],
  allies: [
    "12345",
  ],
};

const root = protobuf.loadSync("file.proto");

const Person = root.lookupType("file.Person");

var simonBuf = Person.create(simon);
var lucasBuf = Person.create(lucas);

const PersonList = root.lookupType("file.PersonList");
const users = PersonList.create({ users: [simonBuf, lucasBuf] });
const dataUsers = PersonList.encode(users).finish();

console.log("proto-serialized=" + dataUsers.length);
console.log("json-serialized=" + JSON.stringify(users).length)
```

The score for proto is 93 bytes, and 207 for JSON.

> [!info] Info
>
> When a protobufjs object like Person or Personlist gets serialised as JSON, like the last line in the script above, then it uses the JavaScript equivalent, so the result is not biased.

With a few more line, we can infer what the proto-serialised message look like:
```js
console.log(dataUsers) // <Buffer 0a 3a 0a 05 31 32 33 34 35 12 05 53 69 6d 6f 6e 1a 07 4c 65 6f 6e 61 72 64 20 15 2a 06 63 6f 64 69 6e 67 2a 07 72 65 61 64 69 6e 67 2a 07 77 72 69 74 ... 43 more bytes>
console.log(dataUser.toString())
//
// :
// 12345SimonLeonard *coding*reading*writing254321
//
// 54321LucasThomas 212345
```

### gRPC

gRPC is "a high performance, open source universal RPC framework"[^grpcio].
RPC stands for *Remote Procedure Call*, which is a kind of API that abstracts the complexity of making calls to a remote API by providing to the developer functions that works like if it was local[^wikipedia].

> [!quote] Quote
> 
> gRPC uses HTTP/2 under the covers, but HTTP is not exposed to the API designer[^googlecloud]

gRPC uses [[research/api/protobuf|Protocol Buffers]] as a serialisation format by default, but can use [[research/api/json|JSON]]. It works by extending the syntax of a `.proto` file.

We need to define the messages (basic Protobuf structure) that will be exchanged whenever it's an argument or a return value, along with the `services`, that describe the procedures available.

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

From the dedicated file, it creates a gRPC server to receive requests from clients that themselves use a dynamic library generated via `protoc` and a dedicated plugin.

![Architecture of gRPC](research/api/grpc-architecture.svg)

This diagram illustrate how different clients/servers written in different languages can work together with the gRPC server and *stubs* (a client) created.

#### File upload

Although a few users would recommend to use a [[research/api/rest|REST]] API to handle uploads, other says that it depends on the size of images and your app[^reddit_grpc_files].

Implementing file uploading in gRPC seems to be done via `stream`s[^betterprogramming][^vinsguru], one of [its features](https://grpc.io/docs/what-is-grpc/core-concepts/#client-streaming-rpc). The core principle is to send files in small chunk (by default, gRPC limits incoming messages to *4 MB* in case the developer hasn't thought about message size, but it can be increased[^sof_4mb]) Note that we send the metadata apart from the file, and it's not a standard so we have to define what's inside (path, name, extension/type...).

Implementations seem to also use `oneof` keyword to avoid sending metadata each time we're sending a chunk of file.
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

## Conclusion

For an app heavily using file transfer, it seems GraphQL is out of the game as no standard exists.

However methods exists in both REST and gRPC, which both have their advantage:
- REST has a well-established mechanic with multipart requests (`multipart/form-data`)
- On the other hand, gRPC doesn't offer a standard, but is flexible enough to implement an upload service. With that, we can choose the metadata that will be transferred, and everything is in one stream.

If we stick to the combo REST/JSON and gRPC/Protobuf, then Protobuf is more efficient and concise, therefore probably more suitable for real-time use.
However, REST/Protobuf is a possible combination (although not often used) and this could profit from the popularity and technologies relying on REST while significantly reducing the request/response bodies.

## Reference

[^grpcio]: _Introduction to grpc_ (2022) _gRPC_. Available at: https://grpc.io/docs/what-is-grpc/introduction/ (Accessed: January 14, 2023).
[^wikipedia]: _Remote procedure call_ (2023) _Wikipedia_. Wikimedia Foundation. Available at: https://en.wikipedia.org/wiki/Remote_procedure_call (Accessed: January 14, 2023).
[^googlecloud]: Nally, M. (2020) _GRPC vs rest: Understanding grpc, openapi and rest and when to use them in API design | google cloud blog_, _Google_. Google. Available at: https://cloud.google.com/blog/products/api-management/understanding-grpc-openapi-and-rest-and-when-to-use-them (Accessed: January 14, 2023).
[^reddit_grpc_files]: _r/grpc - grpc is suitable to handle file uploads?_ (no date) _reddit_. Available at: https://www.reddit.com/r/grpc/comments/oj2k9n/grpc_is_suitable_to_handle_file_uploads/ (Accessed: January 14, 2023).
[^betterprogramming]: Foong, N.W. (2022) _GRPC file upload and download in Python_, _Medium_. Better Programming. Available at: https://betterprogramming.pub/grpc-file-upload-and-download-in-python-910cc645bcf0 (Accessed: January 14, 2023).
[^vinsguru]: vIns (2022) _GRPC file upload with client streaming_, _Vinsguru_. Available at: https://www.vinsguru.com/grpc-file-upload-client-streaming/ (Accessed: January 14, 2023).
[^sof_4mb]: Anderson, E. (2019) _Should I transmit large data sets via grpc without manual chunking?_, _Stack Overflow_. Available at: https://stackoverflow.com/a/58435745 (Accessed: January 14, 2023).
[^aimuliple]: Ataman, A. (2023) _Graphql vs. rest in 2023: Top 4 advantages & disadvantages_, _AIMultiple_. Available at: https://research.aimultiple.com/graphql-vs-rest/ (Accessed: January 13, 2023). 
[^wundergraph]: Neuse, J. (2021) _GraphQL file uploads - evaluating the 5 most common approaches_, _WunderGraph_. Available at: https://wundergraph.com/blog/graphql_file_uploads_evaluating_the_5_most_common_approaches (Accessed: January 13, 2023).
[^multipart]: libik and kirk (2020) _REST API - file (ie images) processing - best practices_, _Stack Overflow_. Available at: https://stackoverflow.com/questions/33279153/rest-api-file-ie-images-processing-best-practices (Accessed: January 13, 2023).
[^33-percent]: T, D. and B, S. (2019) _Posting a file and associated data to a restful webservice preferably as JSON_, _Stack Overflow_. Available at: https://stackoverflow.com/a/4083908 (Accessed: January 5, 2023).
