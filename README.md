## Agile Software Practice - Assignment 1

## *Demo*
__Name:_Emily Halley 20092335
_Demo:_https://youtu.be/rrLJSsuHFqk

## *Showing my knowledge of using Docker by containerizing a multi-container application*
This repository contains the containerization of the multi-container applicaton illustrated below.
This assignment demonstrates how to take a multicontainer application and containerzing it by writing up a docker compose file to 
enable to start up a multi-container application to run and hooking them up together.
It shows how to intergrate all of the different parts altogether for the application.

## *Applicaton Archtecture*
![alt text](<Agile_Software_Practice_Docker_1 (2).jpg>)

The application contains four different types of containers 
- ***Movies Application(doconner/movies-api:10)***
   - This image is provided for Movies Web API and receives incoming HTTP requests for a list of movies at http://localhost:9000/movies or a particular id of a movie at http://localhost:9000/movies/67277ed2ada94e64ce037c97, where the API stores the information.

- ***MongoDB Database(mongo:8.0-rc)***
    - The movie information is stored in the MongoDB database.

- ***Mongo Express Web Application(mongo-express:1.0-20-alpine3.19)***
    - Controls the actions for the MongoDB as a front-end web application

- ***Radis Server(redis:alpine)***
    - Provides two roles in the architecture, a high performance caching server that checks the response from the API for a particular movie and sends it to the MongoDB and returns back to the client and store it in the redis server. Secondly it acts as a rate limiter requester that returns the "get all movies" from the Database for short grace period of 3 to 4 minutes. Also when the request reaches to 11 it will limit to 10 at the upper limit. It knows when to stop responding and will reset the counter back to 0 to enable more requests to come in again and repeat the process.

    - Radis two important roles enables the *movies-api* to talk to *redis* and *mongodb*. 
<br>

## *Tasks done for this assignment*


### Step 1: Getting Started
- Pulled the movies API image: docker pull doconnor/movies-api:1.0
- Downloaded the archive and unzipped the file as the assignment folder.
- Created a repo for the assignment and named it docker-assignment-1a.
- Initalized the project and pushed docker-compose.yml, seeding.json, README.md, .gitignore and .dockerignore. 
- Tested the repo to check if it committed by modifying the README.md and it was a success.

### Step 2: Setting up the basic application
- Merged the docker-compose.yml from docker-profile application repo into the assignment folder.
- File contained the two main containers MongoDB and Mongo-express combining with the initial movies API.
- Verifed that mongo and mongo-express worked by running and building the container docker-compose up --build.

### Step 3: Environment variables, Configuring Redis Stack fully intergrated
- Environment variables added for
  - MongoDB- URL server the database server: MONGODB_URI=mongodb://admin:secret@database:27017 
  - Redis- Default URL server for caching: URI REDIS_URI=redis://redis
  - API is configured to READ-ONLY-MODE from the MongoDB:ENABLE_WRITING_HANDLERS=false
  - Movies API on port 9000:9000 as needed to open on the host.
- Configured Redis in the YAML file by running docker-compose up --build everything loaded up at
    - http://localhost:9000/ : Movies API pinging the client ensuring that they are connected.
    - http://localhost:9000/movies : Empty array of movies but verfied that connection worked.
    - http://localhost:8081/: Manually created a database and added some movie data from seeding.json to test for the movie data to loadup.
    - http://localhost:9000/movies : List of movies manually inputted loaded up. 
    - http://localhost:9000/movies/67277edbada94e64ce037c98 : Took the id of the movie and it worked.
- Killed the application by running docker-compose down.

### Step 4: Container Isolation
- Formatted the file to configure each network container so that:
  - The Mongo-express app is not accessible from either the Redis or Movies API container.
  - The MongoDB container is not accessible from the Redis container.
  - Therefore no communication between those containers, no possible way to ping each other.
    - mongodb-network:, mongodb-ex-network: and redis-network:

### Step 5: Database Seeding
- With the inital data set up, stack deployed and database seeding.
- Implemented the seeding for creating a database and adding the movies collection automatically.
- A database and collection was added and did a test following similar to **Step3**
- Ran docker-compose up --build 
    - http://localhost:9000/ : Movies API pinging the client ensuring that they are connected.
    - http://localhost:9000/movies : List of movies loaded up
    - http://localhost:8081/ : Verified that the database and collection were added
    - http://localhost:9000/movies/67277e99ada94e64ce037c93 : Took the id of the movie and it worked.
- Killed the application by running docker-compose down.

### Step 6: Development and Production Stack options -> Multi-Stack
When you are running the docker compose up we are only working in one environment and the multicontainer needs to be divided into development and production environments.
- For development the application will remain the same as it has always been i.e what was implemented between ***Steps 1 to 5***. 
- In production we need the YAML file to be modfied so that when it composes up it only runs the Movies API, Redis server and MongoDB server **NOT** MONGO-EXPRESS and Mongo App with no seeding in the database. 

These are only for developers who have access to these particular files and can Create, Read, Update and Delete data known as **CRUD**. 
As production is a live system and if these containers were deployed it would be exposing data to the public and cause many security breaches. 
Therefore, it vital that those containers in production are not seeded or deployed in **ANY CONTEXT**.

- Updated the stack to interchange between dev and prod profiles.
- Updated the .env file to switch between stacks.
- Ran docker-compose --profile dev up and docker-compose --profile prod up one at time.
- Tested and worked sucessful between the two profiles.
- Removed prod profile from mongo-express.
- Ran docker-compose --profile prod up and Movies API, Redus server and MongoDB only ran omitting mongo-express.
- Checked docker-compose --profile dev up and everything ran smoothly all four containers.
- Tested this a couple of times and everything was sucessful.


<br>

## *Resources*
Thanks to all of these resources I was fully able to complete this assignment and including the labs

https://tutors.dev/course/agile-practice-setu-deise
https://app.diagrams.net/
https://scribehow.com/shared/How_to_Push_a_Folder_to_GitHub__8xKIoyDGRKqs0295yVhpeg
https://stackoverflow.com/questions/44513786/error-on-mongodb-authentication
https://stackoverflow.com/questions/31210973/how-do-i-seed-a-mongo-database-using-docker-compose
https://docs.docker.com/engine/network/
https://docs.docker.com/compose/how-tos/networking/
https://stackoverflow.com/questions/tagged/docker-networking
https://www.google.com/search?q=what+is+the+port+for+redis&rlz=1C1ONGR_enIE1045IE1045&oq=what+is+the+port+for+redis+&gs_lcrp=EgZjaHJvbWUyCQgAEEUYORifBTIHCAEQIRigATIHCAIQIRigATIHCAMQIRigATIHCAQQIRigATIHCAUQIRigAdIBCDc0MTVqMGo3qAIAsAIA&sourceid=chrome&ie=UTF-8
https://stackoverflow.com/questions/46913047/how-to-put-seed-data-into-sql-server-docker-image
https://forums.docker.com/t/seeding-data-volume-containers-mongodb/2214
https://www.baeldung.com/ops/docker-compose-seed-mongodb
https://platformengineers.io/blog/seeding-a-mongo-db-database-using-docker-compose/
https://stackoverflow.com/questions/48178870/import-data-on-mongodb-using-docker-compose
https://www.mongodb.com/docs/database-tools/mongoimport/
https://medium.com/swlh/dockerizing-a-mongo-database-ac8f8219a019
https://stackoverflow.com/questions/42912755/how-to-create-a-db-for-mongodb-container-on-start-up
https://www.tonic.ai/blog/dockerizing-and-seeding-your-development-mongodb-database
https://collabnix.com/leveraging-compose-profiles-for-dev-prod-test-and-staging-environments/
https://dev.to/iamzahid/mastering-in-production-grade-docker-compose-file-41gm
https://docs.docker.com/engine/containers/resource_constraints/
https://www.geeksforgeeks.org/how-to-seed-a-mongodb-database-using-docker-compose/
https://docs.docker.com/reference/compose-file/
https://docs.docker.com/reference/compose-file/deploy/
https://www.geeksforgeeks.org/docker-compose-yaml-explained-a-deep-dive-into-configuration/
https://docs.docker.com/compose/how-tos/environment-variables/set-environment-variables/
https://vsupalov.com/docker-arg-env-variable-guide/
https://docs.docker.com/reference/cli/docker/compose/
https://docs.docker.com/compose/how-tos/profiles/
https://docs.docker.com/compose/how-tos/environment-variables/best-practices/
https://docs.docker.com/compose/how-tos/environment-variables/envvars/
https://docs.docker.com/compose/how-tos/environment-variables/
https://docs.docker.com/compose/
https://dev.to/jsheridanwells/dockerizing-a-mongo-database-4jf2
https://www.mongodb.com/developer/products/mongodb/mongoimport-guide/
https://stackoverflow.com/search?q=docker+seeding&s=b280065c-6e36-4a93-8d20-62f4db8bd10b
https://docs.docker.com/compose/how-tos/production/
https://www.mongodb.com/docs/manual/reference/default-mongodb-port/
https://docs.docker.com/guides/pre-seeding/
https://stackoverflow.com/questions/19085610/seeding-mysql-data-in-a-docker-build
