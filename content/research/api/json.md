---
title: "JSON"
draft: false
tags: []
aliases: []
---

> [!quote] From the official website
>
> **JSON** (JavaScript Object Notation) is a lightweight data-interchange format. It is easy for humans to read and write. It is easy for machines to parse and generate.
> 
> <small>_Introducing json_ (no date) _JSON_. Available at: https://www.json.org/json-en.html (Accessed: January 13, 2023).</small>

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
			"allies": [
				"12345"
			]
		}
	]
}
```

We can see that JSON as at least one drawback: the document's structure and the data are mixed. You can see above that we repeat the keys `id`, `name`, `surname`... for multiple users.

> [!question] Is it possible to transfer a file over JSON?
>
> Although protocols like REST and gRPC are more suitable for the task (with multipart upload and stream respectively), one could upload a file by encoding it in base64. Note that it increase the data size by ~33%[^33-percent]

[^33-percent]: https://stackoverflow.com/questions/4083702/posting-a-file-and-associated-data-to-a-restful-webservice-preferably-as-json