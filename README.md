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

