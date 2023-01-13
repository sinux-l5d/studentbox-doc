---
title: "graphql"
draft: false
tags: []
aliases:
- GraphQL
---

> [!quote] According to the official website
>
> GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools.
>
> <small>_GraphQL | A query language for you API_ (no date) _GraphQL_. Available at: https://graphql.org/ (Accessed: January 13, 2023).</small>

First thing we notice is that GraphQL is not just a API specification but a full runtime[^aimuliple] unlike [[research/api/rest|REST]] which is just a specification.

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

Those type definition being in separate files, it's easy to document the API automatically with tools like [GraphiQL](https://github.com/graphql/graphiql).

One of the advantages of this query approach is its precision, allowing to have all the data we need at once, without the need to fetch multiple times like it's the case for REST.

## File upload

Just like for REST, we could encode files in base64 to upload them. But that adds overhead for both server and client, increase the file size by ~33%[^wundergraph].

Just like for REST, we could use a multipart request but:

> [!quote] Quote from Jens Neuse[^wundergraph]
>
>  Unfortunately, there's no standard to handle Multipart Requests with GraphQL. This means, your solution will not be easily portable across different languages or implementations and your client implementation depends on the exact implementation of the server.

So one of the other approach is to have another homemade API in REST that takes care of files only, or using AWS S3. The last option is not viable if we want to keep our system independent from cloud.

The article I relied on is made by Jens Neuse at [WunderGraph](https://wundergraph.com), described as "The simplicity of RPC withthe power of GraphQL" on there website. That could be a solution, but would lock us in a not-so-well-known solution, and we don't control the prices. If I where to use RPC, I would probably go with [[research/api/grpc|gRPC]] which is free, opensource and licensed Apache-2.0.

[^aimuliple]: Ataman, A. (2023) _Graphql vs. rest in 2023: Top 4 advantages & disadvantages_, _AIMultiple_. Available at: https://research.aimultiple.com/graphql-vs-rest/ (Accessed: January 13, 2023). 
[^wundergraph]: Neuse, J. (2021) _GraphQL file uploads - evaluating the 5 most common approaches_, _WunderGraph_. Available at: https://wundergraph.com/blog/graphql_file_uploads_evaluating_the_5_most_common_approaches (Accessed: January 13, 2023).