---
title: 👨‍💻 Studentbox documentation
enableToc: false
---

_Studentbox_ is a project that aims to make deployment easy for student 👨‍🎓, and management of hosted projects simple for a teacher 👨‍🏫.

# 🌍 Context

I've realised this project during my BSc (Hons)[^bsc] at [Dundalk Institute of Technology](https://www.dkit.ie/). The curriculum is about _Cloud Computing_, and so this project has to take advantage of different cloud aspects like including scaling, APIs[^api] and automation etc.

See the [[about-me|About me]] page this know more about the author.

# 🥜 In a nutshell...

_Studentbox_ wants to archive the following goals:

- Host the students' projects in an isolated environment, without impacting other projects or host system
- Provide a complete environment for the apps supported
- Be able to deploy in a single command line...
  - PHP app using MySQL
  - Node.js app using MongoDB or MySQL
- The _CLI_[^cli] client should...
  - work on Linux, Mac and Windows
  - be easy to setup
  - be usable by both student and teacher
- Make possible live-reloading while developing

While having the following constraints:

- Hostable on AWS
- Self-hostable on an on-premise server

# 🏁 Where to start

You can access the [[project/goals|goals]] page to know more about the project's goals and the [[project/road-map|road map]].
There is also the [[tags/journal|journal entries]].

[^bsc]:
    **B**achelor of **Sc**ience (**Hon**our**s**) is the 4th year after secondary school (in Ireland).
    In France, the equivalent year is the 1st year of Master's degree (the 4th year after _Lycée_).

[^api]: **A**pplication **P**rogramming **I**nterface in IT is an abstraction layer to interact with things we don't want to deal manually with. In this curriculum, we're talking about Web APIs (REST with JSON among others).
[^cli]:
    **C**ommand **L**ine **I**nterface, as the opposit of Graphical User Interface (GUI) is a text-based interface.
    On Windows, it's in CMD/PowerShell. On Linux, it's in your favourite terminal & shell (bash, zsh...)
