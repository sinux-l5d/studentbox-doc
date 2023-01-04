---
title: "json"
draft: true
tags: []
aliases: []
---

The official website present it as:

> [!quote]
>
> **JSON** (JavaScript Object Notation) is a lightweight data-interchange format. It is easy for humans to read and write. It is easy for machines to parse and generate.
> <small>*JSON introduction. (n.d.). JSON. Retrieved December 14th, 2022, from https://www.json.org/json-en.html*</small>

It's a serialisation format that is human-friendly, meaning object, arrays and values are represented in a string. Example:

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
			allies: [
				"12345"
			]
		}
	]
}
```

We can see that JSON as at least one drawback: the document's structure and the data are mixed. You can see above that we repeat the keys `id`, `name`, `surname`... for multiple users.