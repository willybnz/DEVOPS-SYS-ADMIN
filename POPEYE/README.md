#

## POPEYE 
<p align="center">
  <img src="pictures/Portrait.popeye.png" alt="popeye">
</p>


Popeye is a project that aims to introduce you to the basics of docker and docker compose.
The goal of the project is to containerize and deploy a simple web application.

The web application works a bit like this:

Poll (Flask/Python): A web interface that allows users to vote.
Redis (Temporary database): Stores votes before they are processed.
Worker (Java/Maven): Retrieves votes from Redis and saves them in PostgreSQL.
PostgreSQL (Persistent database): Stores votes permanently.
Result (Node.js): A web interface that displays the results of the votes.

![application](pictures/application.png)

The different codes are provided to you in the pool, result and worker folders.

## CONFIGURATION 

The pool, worker and result elements must be configured using specific environment variables described below:

`Poll`

- [x] REDIS_HOST: the hostname of the Redis service to connect to.

`Result`

✓ POSTGRES_HOST: the hostname of the database service to connect to. <br>
✓ POSTGRES_PORT: the port the database service is listening on. <br>
✓ POSTGRES_DB: the name of the PostgreSQL database to connect to. <br>
✓ POSTGRES_USER: the user that will be used to connect to the database.<br>
✓ POSTGRES_PASSWORD: the password of the user that will be used to connect to the database.<br>

`Worker`

The Worker uses the same environment variables as both Poll and Result.<br>

## DOCKER IMAGES

You have to create 3 images.
The specifications for each image are as described below.
`Poll`

- The image must be based on a Python official image.
- The dependencies of the application can be installed using the following command:
- pip3 install -r requirements.txt
- The application must expose and run on the port 80, and can be started with:
- flask run --host=0.0.0.0 --port=80

`Result`

- The image must be based on an official Node.js version 20 Alpine image.
- The application must expose and run on the port 80.
- The dependencies of the application can be installed using the following command:
- npm install

`Worker`

- The image will be built using a multi-stage build.
- First stage - compilation
- The first stage must be based on maven:3.9.6-eclipse-temurin-21-alpine and be named builder.
- It must be used to build (of course) and package the Worker application using the following
commands:
    mvn dependency:resolve, from within the directory containing pom.xml;
    then, mvn package, from within the directory containing the src directory.

It generates a file in the target directory named worker-jar-with-dependencies.jar (relative to your
WORKDIR).
Second stage - run
The second stage must be based on eclipse-temurin:21-jre-alpine.
This is the one really running the worker using the command:
java -jar worker-jar-with-dependencies.jar

## DOCKER COMPOSE 

You now have 3 Dockerfiles that create 3 isolated images.
It is now time to make them all work together using Docker Compose!
Create a docker-compose.yml file that will be responsible for running and linking your containers.
Your Docker Compose file should contain:
5 services:
✓ poll:
– builds your poll image;
– redirects port 5000 of the host to the port 80 of the container.
– correctly sets the necessary environment variable.
✓ redis:
– uses an existing official image of Redis 7;
– opens port 6379.
✓ worker:
– builds your worker image;
– correctly sets the same environment variables as both the poll and result services.
✓ db:
– represents the database that will be used by the apps;
– uses an existing official image of PostgreSQL 16;
– has its database schema created during container first start;
– correctly sets the appropriate environment variables.
✓ result:
– builds your result image;
– redirects port 5001 of the host to the port 80 of the container;
– correctly sets the necessary environment variables.

Databases must be launched before the services that use them, because these services
are depending on them.

3 networks:
✓ poll-tier to allow poll to communicate with redis.
✓ result-tier to allow result to communicate with db.
✓ back-tier to allow worker to communicate with redis and db.
You must use networks. Using the links property is forbidden.
1 named volume:
✓ db-data which allows the database’s data to be persistent, if the container dies.
The path of data in the official PostgreSQL image is documented on Docker Hub.

Once your docker-compose.yml is complete, you should be able to run all the services and access the
different web pages:
✓ Poll at http://localhost:5000;
✓ and Result at http://localhost:5001.
You should be able to vote on Poll’s page and see its effect on Result’s page.
Your containers must restart automatically when they stop unexpectedly.
