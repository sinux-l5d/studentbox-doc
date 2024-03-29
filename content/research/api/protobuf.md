---
title: "Protocol Buffers"
draft: false
tags: []
aliases:
  - Protobuf
  - Protocol Buffers
---

[Protobuf or Protocol Buffers](https://developers.google.com/protocol-buffers) is a binary serialisation format that needs type definition beforehand. It's created by Google. This allows to omit structure from the data serialised, as client and server know what it is about.

> [!quote]
>
> Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.
> <small>_Protocol buffers | google developers_ (no date) _Google_. Google. Available at: https://developers.google.com/protocol-buffers (Accessed: January 13, 2023).</small>

To demonstrate how it's working, I've recreated the same structure as in the [[research/api/json|JSON]] page.

A _type_ in protobuf is called a `message`. Messages are defined in a `.proto` file. Let's create a list of people, and see how it's serialised compared to JSON.

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
const protobuf = require("protobufjs")

const simon = {
  id: "12345",
  name: "Simon",
  surname: "Leonard",
  age: 21,
  hobbies: ["coding", "reading", "writing"],
  allies: ["54321"],
}

const lucas = {
  id: "54321",
  name: "Lucas",
  surname: "Thomas",
  age: 22,
  hobbies: [],
  allies: ["12345"],
}

const root = protobuf.loadSync("file.proto")

const Person = root.lookupType("file.Person")

var simonBuf = Person.create(simon)
var lucasBuf = Person.create(lucas)

const PersonList = root.lookupType("file.PersonList")
const users = PersonList.create({ users: [simonBuf, lucasBuf] })
const dataUsers = PersonList.encode(users).finish()

console.log("proto-serialized=" + dataUsers.length)
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
