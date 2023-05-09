---
title: "Research Methodology Report"
draft: false
tags: []
aliases: []
---

# Introduction

Studentbox is a CLI tool that aims at automating the creation of sandboxed environments (isolated from each other, and from host). The default environment (called runtime) is the traditional LAMP stack most students go through to learn PHP and SQL.
The project is using Podman 4, which has to be installed with a running user socket.

In this paper, we will test this runtime to determine whether it is working as it would work with a manual install of a LAMP stack.

We'll cover the following questions:
- Is spawning a runtime simple?
- Can we check that the containers are up and running?
- Does it create the necessary folders?
- Does it give the credentials for the database, and the port to access the web server?
- Does a piece of PHP code work as expected?
- Is the DB connection working?

To answer these questions, we have chosen to use a qualitative approach. This is because we believe that a qualitative approach will allow us to gain a good understanding of the technical process of spawning and testing the LAMP runtime. We do not expect to collect any relevant data beyond this understanding.

# Research Objective and Methodology

This research aims to evaluate the LAMP runtime on Studentbox and compare its functionalities to a manual LAMP stack installation, utilising a concise qualitative research methodology.

Qualitative research is well-suited for examining technical processes, as it enables in-depth data collection and analysis of one's own experiences, observations, and reflections. By employing this approach, a thorough understanding of the LAMP runtime within Studentbox can be achieved.

Data collection will involve various methods, including observation, documentation (log, error messages etc.) and testing. We will assess the simplicity of spawning a runtime, check the status of containers, verify folder creation, verify if information is provided, test PHP code execution, and examine database connections. These techniques will yield valuable data on the LAMP runtime's functionality, strengths, and weaknesses.

A case study research design will be used to explore the LAMP runtime on Studentbox in detail, uncovering factors contributing to its success or failure. Ultimately, our qualitative methodology and case study approach will provide in-depth insights into this critical aspect of the Studentbox platform.

# Data collection

We'll answer each question using Studentbox `0.2.0`.

## Is spawning a LAMP runtime simple?

Spawning a LAMP stack with the studentbox cli, meaning a pod of containers, is done as follows:

```
$ ./studentbox spawn --user simon --project hello-world --runtime lamp
[containers] 2023/05/09 16:01:22 INFO: Created pod a5c511d24ba44f20c4111fdd58066c78d046ea00f6bb136ec4ff1471034c5175
[containers] 2023/05/09 16:01:22 INFO: Created container 90acb2d5b4b859bbc2a15fe4ec9573f5b3daa5d76d6d2feef6e280c03d8b0d94 in pod a5c511d24ba44f20c4111fdd58066c78d046ea00f6bb136ec4ff1471034c5175
[containers] 2023/05/09 16:01:22 INFO: Created container 48fa3b1765b3a77a89bef60fac2917fead010e3184aa4d2988747aa5bb30dd24 in pod a5c511d24ba44f20c4111fdd58066c78d046ea00f6bb136ec4ff1471034c5175
[containers] 2023/05/09 16:01:22 INFO: Created container aae609d91557fc5a6551b64432663c1b0af20a8ffb7b409362b44c1c4f43d399 in pod a5c511d24ba44f20c4111fdd58066c78d046ea00f6bb136ec4ff1471034c5175
```

To create a runtime instance, called a project, the CLI requires a username, a project name, and a runtime name.

The spawning is simple, but as the time of writing the output is not so relevant, giving a bunch of IDs of containers started.

## Can we check that the containers are up and running?

There are two ways to check the health of Studentbox's containers.

First one is global to all containers, and check for containers currently running:
```
$ ./studentbox list
Containers:
- a5c511d24ba4-infra (simon/hello-world)
- sb-simon-hello-world-mysql (simon/hello-world)
- sb-simon-hello-world-php (simon/hello-world)
- sb-simon-hello-world-apache (simon/hello-world)
```

Second is project-scoped:
```
$ ./studentbox status --user simon --project hello-world
Status of project simon/hello-world:
- a5c511d24ba4-infra: running
- sb-simon-hello-world-mysql: running
- sb-simon-hello-world-php: running
- sb-simon-hello-world-apache: running
```

So yes, we can check the containers are up and running.

## Does it create the necessary folders?

By default, the CLI uses the data directory in the current directory to store all data. Let's check it:

```
$ tree -d
.
└── data
    └── simon
        └── hello-world
            ├── db
            │   ├── app  [error opening dir]
            │   ├── mysql  [error opening dir]
            │   ├── performance_schema  [error opening dir]
            │   └── sys  [error opening dir]
            └── html

10 directories
```

We note that there are errors trying to read the `db` directory, which is a mount point for the MariaDB container.

After checking the permissions of the folder:
```
$ ls -l data/simon/hello-world/db
total 139392
drwx------ 2 10998 10998      4096 May  9 16:01 app
```

It seems that the group and id does belong to my user's subrange:
```
$ cat /etc/subuid
sinux:10000:65536
```

It's a limitation that can we worked-around with a `podman` utility command:
```
$ podman unshare tree -d
.
└── data
    └── simon
        └── hello-world
            ├── db
            │   ├── app
            │   ├── mysql
            │   ├── performance_schema
            │   └── sys
            └── html

10 directories
```

Although the permissions seem to limit the native interactions with the file-system (for the database), yes the files and folders are created.

## Does it give the credentials for the database, and the port to access the web server?

At the time of writing, Studentbox `v0.2.0` doesn't offer a convenient way to retrieve the random passwords or the port used to access the runtime.

We can however do it with Podman itself, which should be installed anyway to use studentbox.

To get the port: 
```
$ podman ps --filter label="studentbox.container=true" --format "{{ .Names}}: {{ .Ports }}"
a5c511d24ba4-infra: 0.0.0.0:42765->80/tcp
sb-simon-hello-world-mysql: 0.0.0.0:42765->80/tcp
sb-simon-hello-world-php: 0.0.0.0:42765->80/tcp
sb-simon-hello-world-apache: 0.0.0.0:42765->80/tcp
```
Here we see that my pod is exposing the port 42765.

To get the passwords for MariaDB:
```
$ podman inspect sb-simon-hello-world-mysql --format '{{- range $e := .Config.Env }}{{ $e }}{{"\n"}}{{- end}}' | grep MARIADB
MARIADB_DATABASE=app
MARIADB_VERSION=1:10.9.5+maria~ubu2204
MARIADB_PASSWORD=uJBlWQNf1D
MARIADB_ROOT_PASSWORD=DH7zfSJyBuMTZxx7RWGdWMdCn73ZDm
MARIADB_USER=student
```
Here, the password to give to a student would be `uJBlWQNf1D`.

So no, Studentbox fails at providing necessary information, for now.

## Does a piece of PHP code work as expected?  Is the DB connection working?

Here we'll try the following php code:
```php
<?php

// Testing date

echo "Today is " . date("d-m-Y H:i:s") . "<br/>";

// Testing database connexion

$c = mysqli_connect("127.0.0.1:3306", "student", "uJBlWQNf1D", "app");

if ($c -> connect_errno) {
  echo "Failed to connect to MySQL: " . $c -> connect_error . "<br/>";
  exit();
} else {
  echo "Connected to MySQL <br/>";
}

$query = $c->query("SHOW DATABASES");

echo "Databases: ";

foreach ( $query->fetch_all() as $row) {
  echo $row[0] . " ";
}

// Getting php info
phpinfo();
```

That we put in the `./data/simon/hello-world/html/index.php` file.

It results in the following:

![[project/lamp_result.png]]

Here we learn several things:
1. Date is working, displayed with the UTC time by default
2. We can access MySQL/MariaDB, and 2 databases exists, the `app` one being the default one for students
3. Finally, we get all php informations, including the version (8.2.3 is latest at the time of writing) and the details for each php module (not in the image)

Furthermore, we can try to write in the database with the following:

```php
<?php

$conn = mysqli_connect("127.0.0.1:3306", "student", "uJBlWQNf1D", "app");

if ($conn -> connect_errno) {
  die("Failed to connect to MySQL: " . $c -> connect_error . "<br/>");
}

// Create a new table called "users" with two columns: "id" and "name"
$sql = "CREATE TABLE IF NOT EXISTS users (id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY, name VARCHAR(30) NOT NULL)";
if ($conn->query($sql) === TRUE) {
    echo "Table 'users' created successfully<br>";
} else {
    echo "Error creating table: " . $conn->error;
}

// Insert a new row into the "users" table
$sql = "INSERT INTO users (name) VALUES ('John Doe')";
if ($conn->query($sql) === TRUE) {
    echo "New record created successfully<br>";
} else {
    echo "Error: " . $sql . "<br>" . $conn->error;
}

// Select all rows from the "users" table and display them
$sql = "SELECT * FROM users";
$result = $conn->query($sql);

if ($result->num_rows > 0) {
    while($row = $result->fetch_assoc()) {
        echo "id: " . $row["id"] . " - Name: " . $row["name"] . "<br>";
    }
} else {
    echo "0 results";
}

// Close the MySQL connection
$conn->close();
```

That works:
```
$ curl http://localhost:42765 -s | sed -e 's/<br>/\n/g'
Table 'users' created successfully
New record created successfully
id: 1 - Name: John Doe
```

So yes, we can execute PHP code and work with the database.

# Preliminary Data Analysis

The analysis of the collected data from Studentbox 0.2.0 can be summarised as follows:

1. Spawning a LAMP runtime is simple but generates less relevant output, consisting mainly of container IDs.
2. The CLI allows checking the status of containers globally and project-wise, confirming they are up and running.
3. The necessary folders are created, but native interactions with the database's folder are limited due to permission issues. However, using `podman unshare`, as [stated in its documentation](https://docs.podman.io/en/latest/markdown/podman-unshare.1.html) seems to avoid the issue for now.
4. Studentbox does not provide database credentials and web server port access directly at the time of writing. However, these can be retrieved using the installed `podman` CLI tool.
5. PHP code execution and database connections work as expected, with the tested code successfully interacting with the MySQL/MariaDB database.

In conclusion, Studentbox 0.2.0 successfully deploys the LAMP runtime, allowing the execution of PHP code and interaction with the database. However, there are areas for improvement, such as the output relevancy, access to credentials and port information, and native file system interaction.

# Reflection and Limitations

As we think about our study, we become aware of a number of constraints that might have affected the outcomes and the applicability of our findings.

1. Time restrictions: Because the investigation was done in a short amount of time, it's possible that we were unable to thoroughly examine every part of the Studentbox LAMP runtime or run more tests to confirm our findings.

2. Limited resources: we only tested Studentbox on Arch Linux; we did not test it on any other platforms or operating systems. The applicability of our findings to different environments may have been impacted by this restriction.

3. Limited comments from inexperienced users: In our study, we excluded inexperienced users and anyone unfamiliar with the LAMP stack or Studentbox. Their opinions might have offered insightful information about the usability and efficiency of the LAMP runtime on Studentbox from the viewpoint of a novice user.

4. Lack of external input: We did not speak with a technician or a teacher who could have provided opinions on the installation procedure or suggested modifications to the Studentbox LAMP runtime. Their viewpoint might have added to our comprehension of the system's advantages and disadvantages.

In summary, these constraints should be taken into account when interpreting our results, even though our study has given useful insights into the LAMP runtime on Studentbox. By including a wider variety of participants, running experiments on other platforms, and getting feedback from people with varied levels of experience and competence, future study could solve these constraints. This would improve our comprehension of the use of Studentbox's LAMP runtime and provide information for future system upgrades.

# Conclusion

In this study, we investigated the functionality of the LAMP runtime on Studentbox. Through our qualitative research methodology, we were able to gain valuable insights into the system's strengths and weaknesses. Despite the limitations of our study, the findings offer an understanding of the LAMP runtime on Studentbox and its potential for use in an educational environment.

Future research should address the limitations we encountered by involving a diverse range of participants, testing on various platforms, and seeking feedback from users with different levels of experience.
This will help to build a more comprehensive understanding of the LAMP runtime on Studentbox and contribute to the ongoing development and improvement of the software for the benefit of students and teachers alike.