---
title: "Goals"
draft: false
tags: 
- project
---

# Back end

The back end should be either hosted on AWS or an on-premise server.

Amazon's cloud would be useful if one needs to easily scale as more students use it.
On another hand, an on-premise server allow flexibility and complete control other data, privacy and hosting.

This means that I cannot use AWS's specific service, or at least not without increasing development cost.

Finally, an easy/automated installation would save time.

## Isolate process

We don't want that students' project impact other's by accident or on purpose.

If a web app crash, is heavily dealing with I/O, or is stuck in a infinite loop, it should impact as little as possible other projects.

## Data exchange

The API should be bidirectional, for let's say uploading files and getting live logs of the back end.

## Live reload

Live reload is a convenient feature to allow to see changes as soon as a file is saved. In our case, that would mean upload the changed files and eventually restart the webapp.  

## Users & permissions

Student and teachers are both users. Teachers can host his own project, for demonstration purpose let's say. He can invite students to register in his class, and manage the projects associated.

## Git integration 💡

Use Git as part of the API, as a backup/rollback system.
Each project would have a repository.

# CLI

A CLI client for all users. A student should be able to deploy his project, retrieve logs (possibly in live), get link. A teacher has the same rights as student, plus managing resources, users, projects.

Because most users use Windows, the CLI needs to be cross platform.

# Front end

Simple webapp that allow the same thing as CLI, in a more graphical manner. No install, drag and drop zip/archive to deploy. 
Note for the ZIP: mind that code might be at root of the archive, not in a top-level directory.

It should present live logs, just like CLI.
