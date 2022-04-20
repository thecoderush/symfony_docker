## How to Get Started with Docker Compose and Symfony


https://www.twilio.com/blog/get-started-docker-symfony

**It works on my machine**

1. Enter Docker
2. Prerequisites
3. Getting started
    3.1. Create the Docker Compose configuration
    3.2. Define the database container
    3.3. Define the PHP container
    3.4. Define the Nginx container
    3.5. Build the containers
4. Create the Symfony application
    4.1. Create the quote entity
    4.2. Create the quote fixture
    4.3. Creating the quote controller
    4.4. Style the view
5. Test the application
6. That's how to set up a Symfony project using Docker


### 1. Enter Docker

containers[https://www.docker.com/resources/what-container/]

By integrating Docker into your Symfony project, you can be assured that whenever your application is running, the environment and it's configuration will be the same--regardless of where it's deployed.

In this article, I will show to use Docker with a Symfony project. Nginx will be used as the webserver, PHP-FPM will process PHP requests, and MySQL will be the backend database. The application to be built will display famous quotes made by renowned historians over the years.


### 2. Prerequisites

° Previous experience with Symfony, Twig, and ORMs (Specifically Doctrine).

° [series]https://towardsdatascience.com/learn-enough-docker-to-be-useful-b7ba70caeb4b

° Composer globally installed

° Docker Desktop (optional?)

° The Symfony CLI tool.


### 3. Getting started

    $ mkdir symfony_project
    $ cd symfony_docker


#### 3.1 Create the Docker compose configuration

    $ touch docker-compose.yml


In docker-compose.yml, add the configuration below.

    version: '3.8'

    services:

version refers to the schema version. **services** defines the list of containers our application stack will consist of.   


#### 3.2 Define the database container

    *docker-compose.yml*
    {code}


#### 3.3 Define the PHP container

    $ mkdir php
    $ touch php/Dockerfile

    *symfony_docker/php/Dockerfile*
    {code}

Note: In the last two lines of symfony_docker/php/Dockerfile, please replace "you@example.com" with your email address and "Your Name" with your actual name.

In addition to scaffolding a container from the PHP-FPM image, we will do the following:

1.    Install the PHP extensions Symfony depends on.
2.    Set the working directory of the container to /var/www/symfony_docker
3.    Install composer
4.    Install the Symfony CLI

    *docker-compose.yml* for the database configuration.
    {code}


#### 3.4 Define the Nginx container


**!ALERT!**

    The following command: 
    
        $ $ mkdir -p nginx/default.conf

    creates 2 folders unstead of a default.conf file into a nginx folder 

    docker-compose up will make a warning or abort the container build because of non matching type mapping **volumes** 

    You need to execute these instead:
        $ mkdir nginx
        $ touch nginx/default.conf

**!ALERT!**

    $ mkdir -p nginx/default.conf
    
Add the configuration to *nginx/default.conf*
    {code}

This is a basic Nginx configuration required for running a Symfony project.

Next, add the Nginx container's configuration to *docker-compose.yml*
    {code}


#### 3.5 Build the containers

    $ docker-compose up -d --build

**!ALERT!**
    
At this stage you will encounter successful built but both nginx and mysql container will **Exited** code 1 immediatly due to **permission** issue.

    $ docker ps
    $ docker container ls -a

You can log errors information

    $ docker logs nginx
    $ docker logs mysql

Removing all unsued Docker objects
    
    $ docker system prune
    
This command removes all stopped containers, dangling images, and unused networks.

Removing the php container as well 

    $ docker stop php
    $ docker rm php


According to the error information, it is obvious that it is the problem of user permission. That's because **SELinux** (Security Extended Linux) cause "Permission denied" issue using docker.

"I am using docker on RHEL 7. After mounting host directory into container, some interesting things happen: Although I am a root user, and seem to have all permissions, but the system will prompt “Permission denied” when executing commands" (or building with docker-compose in our case)

the root cause is about SELinux:

    $ getenforce
    $ sestatus

SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33


[Debug Ref.]https://qdmana.com/2022/02/202202021639157801.html
[Debug Ref.]https://www.cnblogs.com/activiti/p/7552677.html
[Debug Ref.]https://nanxiao.me/en/selinux-cause-permission-denied-issue-in-using-docker/

The **current mode** of SELinux is **enforcing**, ... resolve it:

Set (as root) SELinux mode as *permissive*:

    $ sudo setenforce 0

...
Current mode:                   permissive
...

Now you can execute docker-compose build command:

    $ docker-compose up -d --build

then,

    $ docker ps

CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                                                  NAMES
ebc8bbd13f3a   nginx:stable-alpine   "/docker-entrypoint.…"   5 seconds ago   Up 3 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp                  nginx
122b4108a83a   symfony_docker_php    "docker-php-entrypoi…"   6 seconds ago   Up 4 seconds   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp              php
c4a8ba129887   mysql:8.0             "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   33060/tcp, 0.0.0.0:4306->3306/tcp, :::4306->3306/tcp   database

Our 3 containers are now up!

**!ALERT!**
