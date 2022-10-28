---
title: "Hugo and Quartz"
---

# Front matter

Front matter is a YAML[^yaml] header required by [Hugo](https://gohugo.io) that should be placed at the very top of every file. The most commons are included in the template (`templates/page`).

For a detailed list of key/values see [this link](https://gohugo.io/content-management/front-matter/).

# Naming conventions

1. The name of the note (the file) is at the top of every notes and is used in quartz as the path (eventually prefixed by parents directories). Therefore, it should stay fairly simple.
2. The title in the [[private/hugo-and-quartz#Front matter|front matter]] is both used in the title of page and tab (in browser). It can be fancy

[^yaml]: [Yet Another Markup Language](https://yaml.org)