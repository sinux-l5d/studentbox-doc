---
title: "REST"
draft: false
tags: []
aliases:
  - REST
---

REST (_Representational state transfer_) is a style of software architecture that takes advantage of HTTP(S):

- The path represent a resource
- A verb represent what kind of operation has to be done
- Headers are used for various things, like authorisation token
- The body for the actual data, when needed, nowadays mostly [[research/api/json]] for APIs
- The return status tells quickly if everything happened as expected

Let's say we have a database of recipes. We want to list them, add, modify and delete them. This can be done with a meaningful base path like `/recipes`. An API following REST principles would have those couples verbs/paths:

- `GET /recipes` to have a list of them (complete or partial objects, maybe with pagination)
- `POST /recipes` to add one
- `PUT /recipes/:id` or `PATCH /recipes/:id` to modify a recipe fully or partially (where id is a unique identifier of the resource)
- `DELETE /recipes/:id` to delete it

## File upload

For uploading files, we have several options. One is to use base64 encoding and put the result in JSON, but it's not the best option for large files. Although we might never have big files (because we exchange coding files), it is something to consider for assets for example.

Another way of doing this through a `multipart/form-data` request[^multipart]. The principle is to first upload the files independently from their metadata or any data you could send. Either first upload the files, get IDs and link them to the metadata (server controls names/ids) or upload metadata first and then files (client controls names/ids).

[^multipart]: libik and kirk (2020) _REST API - file (ie images) processing - best practices_, _Stack Overflow_. Available at: https://stackoverflow.com/questions/33279153/rest-api-file-ie-images-processing-best-practices (Accessed: January 13, 2023).
