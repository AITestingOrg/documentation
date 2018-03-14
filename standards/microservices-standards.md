# INDEX

* [Spring boot minimun requirements.](#minimum-requirements-for-spring-boot-services)
* [Code Style](#code-style)
* [Coverage](#coverage)
* [Docker](#docker)
* [Travis CI](#travis)

[Back](../README.md)

---
# Minimum Requirements for Spring Boot Services

* If accessible via front-end must have routes in Edge service
* Must use Eureka for all communication
* Must have 85% test coverage(See [Coverage](#coverage))
* 100% test coverage on logic
* Authentication profile
* Two docker compose scripts
* Docker Image
* Travis (uploads Docker, Runs tests, test coverage, lint)
* Configured to use configuration server
* All communication via RestTemplate or Events

[Back](#index)

---

# Code Style

Projects should follow well known and stablished code-style guides. 

In the case of Java code the [google style guide](https://google.github.io/styleguide/javaguide.html) is followed except that 4 spaces are used for indentation. 

Java projects should use checkstyle plugin for code style reports and checks. This plugin will by default run on build.

Add the plugin to build graddle:

```groovy
apply plugin: 'checkstyle'
```

The plugin can be configured in a few ways:
```groovy
checkstyle {
	toolVersion = "8.7"
	configFile = new File(rootDir, "checkstyle.xml")
}

checkstyleMain {
	source = fileTree('src/main') {
		excludes = ['*.yml']
	}
}

checkstyleTest {
	source = fileTree('src/test') {
		excludes = ['*.yml']
	}
}
```

* toolVersion is used to specifie the version to use, by default it is an older version.
* configFile is used to specify the xml file with the rules to follow.
* the checkstyleMain and checkstyleTest can be used to customize these two tasks. Files to be excluded can be added.
* A modified version of google's javaguide can be found in [here](checkstyle.xml).

[Back](#index)

---

# Coverage

There should be a code coverage of at least 85% for the code, and 100% for logic in general. 

In java the jacoco pluging can be used to provide checks and integration with builds. This can be configured in gradle like this.

```java
apply plugin: "jacoco"
```

This can be futher configured:

```java
jacocoTestReport {
	reports {
		html.destination file("${buildDir}/jacocoHtml")
	}
}

jacocoTestCoverageVerification {
	violationRules {
		rule {
			limit {
				minimum = 0.85
			}
		}
	}
}
test.finalizedBy(jacocoTestCoverageVerification)
```

This accomplishes:

* Specifying the output for the html report location.
* Define a rule that enforces 85% coverage.
* Tell the test task to run jacocoTestCoverageVerification after it finishes.

[Back](#index)

---

# Docker

## Dockerfile

A Dockerfile file should be added at the root level of the project. This is a sample springboot default Dockerfile:

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD "Replace this with the path to the jar file" app.jar
EXPOSE "ports needed, like 8080"
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```

The path to the output folder jar file should be properly added, for example: 

build/libs/new-project-0.0.1-SNAPSHOT.jar

The exposed port should match the port the applications is cofigured to listen too.

## docker-compose.yml

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

Official documentation and more info can be found [here](https://docs.docker.com/compose/)


## .travis.yml file

Add the needed credentials for docker using travis from the command line:
If travis is not installed, first install it with gem:

```gem install travis```

Then run from the project root folder, reply *yes* when prompted:

```travis encrypt VAR_NAME="value to encrypt" --add```

For the variables DOCKER_USERNAME and DOCKER_PASSWORD as seen in the script below. These values can then be used throught the script.

Add the following script to the .travis.yml file in for the project and add the corresponding docker hub repo for the project in place of \_DOCKERHUB_REPO_. By default only master will be pushed but this can be configured to push depending on other conditions like git tags.

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
  - export REPO=_DOCKERHUB_REPO_
  - export TAG=`if [ "$ACTUAL_BRANCH" == "master" ]; then echo "latest";else echo ""; fi`
  - docker build -f Dockerfile -t $REPO:$TAG .
  - docker push $REPO
```

[Back](#index)

---

# Travis

A .travis.yml file needs to be added to the project. A minimun example for a java project is:

```yml
language: java
jdk:
  - oraclejdk8
encoding: utf8
```

This file can be configured to start services, define environment variables, run scripts, build docker, and other tasks. Other customizations and options can be found [here](https://docs.travis-ci.com/user/customizing-the-build/).

[Back](#index)