---
title: "REST"
draft: false
tags: []
aliases:
- REST
---

REST (*Representational state transfer*) is a style of software architecture that takes advantage of HTTP(S):
- The path represent a resource
- A verb represent what kind of operation has to be done
- Headers are used for various things, like authorisation token
- The body for the actual data, when needed, nowadays mostly [[research/api/json]] for APIs
- The return status tells quickly if everything happened as expected

Let's say we have a database of recipe. We want to list them, add, modify and delete them. This can be done with a meaningful base path like `/recipes`. An API following REST principles would have those couples verbs/paths:
- `GET /recipes` to have a list of them (complete or partial objects, maybe with pagination) 
- `POST /recipes` to add one
- `PUT /recipes/:id` or `PATCH /recipes/:id` to modify a recipe fully or partially (where id is a unique identifier of the resource)
- `DELETE /recipes/:id` to delete it
