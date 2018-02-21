# Guidelines for deploying microservices to docker from the pipeline and dockerizing applications.

## Dockerfile
A Dockerfile file should be added at the root level of the project. This is a sample springboot default Dockerfile:

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD "Replace this with the path to the jar file" app.jar
EXPOSE 8080
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```

The path to the output folder jar file should be properly added, for example: build/libs/new-project-0.0.1-SNAPSHOT.jar
The exposed port should match the port the applications is cofigured to listen too.

## .travis.yml file
Add the needed credentials using travis from the command line:
If travis is not installed or the command is not recognized, first install it with gem:

```gem install travis```

Then run:

```travis encrypt VAR_NAME="value to encrypt"```

These values can then be used throught the script.

Add the following script to the .travis.yml file in for the project and add the corresponding docker hub repo for the project. By default only master will be pushed but this can be configured to push depending on other conditions like git tags.

```yml
before_install:
  - echo "Testing Docker Hub credentials"
  - docker -v
  - docker login -u=$DOCKER_USERNAME -p=$DOCKER_PASSWORD
  - echo "Docker Hub credentials are working"

after_success:
  - export ACTUAL_BRANCH=$(if [ "$TRAVIS_PULL_REQUEST" == "false" ]; then echo $TRAVIS_BRANCH; else echo $TRAVIS_PULL_REQUEST_BRANCH; fi)
  - echo "Test Success - BranchActual($ACTUAL_BRANCH) Pull Request($TRAVIS_PULL_REQUEST) Tag($TRAVIS_TAG) PR($TRAVIS_PULL_REQUEST) Build($TRAVIS_BUILD_NUMBER)"
  - if [[ "$ACTUAL_BRANCH" == "master" ]]; then echo -e "Push Container to Docker Hub"; fi
  - docker login -u=$DOCKER_USERNAME -p=$DOCKER_PASSWORD
  - export REPO="ADD DOCKERHUB REPO"
  - export TAG=`if [ "$ACTUAL_BRANCH" == "master" ]; then echo "latest";else echo ""; fi`
  - docker build -f Dockerfile -t $REPO:$TAG .
  - docker push $REPO

```

## docker-compose

A docker-compose.yml file should be added to the applications root:
This is an example docker compose file which follow the structure:
* version:
    This field is always required.
* services:
    A list of the services needed for the application to run correctly.

The following example spins up mongodb, rabbitmq and the current application as docker containers. 

By not specifying the host ports for the project services it is left to docker compose to figure out what available ports are left. The port assigned to it can be check running ```docker ps``` and checking the PORTS entry for the docker image.  

For example: ```0.0.0.0:32777->8080/tcp``` means that host port 32777 is the port used by the container.

```yml
version: '3'
services:
  mongo:
    image: 'mongo:3.4.1'
    container_name: 'mongo'
    ports:
      - '27017:27017'
    volumes:
      - 'mongo:/data/db'
  rabbitmq:
    image: rabbitmq:management
    container_name: 'rabbitmq'
    ports:
      - "5672:5672"
      - "15672:15672"
  this-project:
    build: ./
    container_name: 'this-project'
    environment:
      - RABBIT_HOST=rabbitmq
      - MONGO_HOST=mongo
    ports:
      - '8080'
    depends_on:
      - rabbitmq
      - mongo
volumes:
  mongo:
```
