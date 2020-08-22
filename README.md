docker pull jaspeen/oracle-11g# Docker: Oracle Database 11g EE

- [Introduction](#Intro)
- [Installation](#Installatione)
- [Configuration](#Configuration)

## Intro

Install oracle 11g Enterprise Edition 

## Installation

###  Install oracle 11g mirror to docker

```bash
-- Search for qualified mirrors
docker search oracle

NAME                                  DESCRIPTION                                     STARS
oraclelinux                           Official Docker builds of Oracle Linux.         680                 
jaspeen/oracle-11g                    Docker image for Oracle 11g database            167
oracleinanutshell/oracle-xe-11g                                                       112
...

-- Choose to install jaspeen/oracle-11g, wait for download and installation to complete
docker pull jaspeen/oracle-11g

-- View downloaded images
docker images

REPOSITORY           TAG                   IMAGE ID            CREATED             SIZE
jaspeen/oracle-11g   latest                0c8711fe4f0f        4 years ago         281MB
```

Note: this image does not install oracle directly. It configures the environment and provides installation scripts. We just need to configure the oracle installation directory and start the image as required.

### Preparing oracle 11g installation files

Download the necessary installation package: [oracle 11g](https://www.oracle.com/database/technologies/112010-linx8664soft.html)
Download two zip packages: 
- linux.x64_11gR2_database_1of2.zip 
- linux.x64_11gR2_database_2of2.zip

unzip them to the home directory (following directory structure)

Example: folder C:/oracleinstall

```bash
C:.
└─oracleinstall
    └─database
        ├─doc
        ├─install
        ├─response
        ├─rpm
        ├─sshsetup
        ├─stage
        ├─runInstaller
        └─welcome.html
```

### Boot Mirror

Interpretation of the command:
- Command for docker run to start container
- privileged gives this container privileges to manipulation of files or directories.
- Name Give this container a name
- p map port
- v Hang file to container specified directory (/oracleinstall for container /install/database)
- jaspeen/oracle-11g starts the specified container

```bash
docker run --privileged --name oracle11g -p 1521:1521 -v c:/oracleinstall:/install jaspeen/oracle-11g

Database is not installed. Installing...
Installing Oracle Database 11g
Starting Oracle Universal Installer...
...
```

Notice that 100% complete prints in the log represent successful oracle installation

### Installation complete

Check the running status, oracle has started and finished

```bash
docker ps -a

CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS                      PORTS                              NAMES
4450c2d64a83        jaspeen/oracle-11g   "/assets/entrypoint.…"   3 hours ago         Up 3 hours                  0.0.0.0:1521->1521/tcp, 8080/tcp   oracle11g
```

## Configuration

The default scott user is locked and we need to unlock it to successfully connect to oracle through the database tools

###  Connect to container

```bash
docker exec -it oracle11g /bin/bash
```

### Switch to oracle user and connect to sql console

```bash

docker exec -it oracle11g /bin/bash

[root@4450c2d64a83 /]# su - oracle
Last login: Sat Aug 22 09:55:20 UTC 2020 on pts/0

-- connect to SQLPlus

[oracle@4450c2d64a83 ~]$ sqlplus / as sysdba

SQL*Plus: Release 11.2.0.1.0 Production on Sat Aug 22 12:47:01 2020

Copyright (c) 1982, 2009, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL>

```

### Unlock Account

```bash
SQL> alter user scott account unlock;
User altered.
SQL> commit;
Commit complete.
SQL> conn scott/tiger
ERROR:
ORA-28001: the password has expired
Changing password for scott
New password:
Retype new password:
Password changed
Connected.
SQL> 

-- Connect to oracle database using DBeaver 
Host        : localhost
Port        : 1521
Database    : orcl
Type        : SID
Username    : scoot
Password    : [password]

```

