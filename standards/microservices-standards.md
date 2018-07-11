# Contents

* [Spring boot minimun requirements.](#minimum-requirements-for-spring-boot-services)
* [Microservice API Standars](#Microservice-API-Standars)
* [Code Style](#code-style)
* [Coverage](#coverage)
* [Docker](#docker)
* [Travis CI](#travis)
* [Backing Service](#backing-service)
* [Logging](#logging)

[Back to README](../README.md)

---
# Minimum Requirements for Spring Boot Services

* If accessible via front-end there must be routes in the [Edge service](#backing-service)
* Must use Eureka for all communication
* Must have 85% test coverage(See [Coverage](#coverage))
* 100% test coverage on logic
* Authentication profile
* Two docker compose scripts
* Docker Image(See [Docker](#docker))
* Travis (uploads Docker, runs tests, test coverage, lint)
* Configured to use configuration server
* All communication via RestTemplate or Events

[Back to top](#list-of-contents)

---

# Microservice API Standards

Guidelines for Microservice API formatting, observing some best practices for RESTful API interfaces.

Reference: https://github.com/WhiteHouse/api-standards

## General

After the name of the server the URL must contain the word "api".

Example: http://localhost/api

## Versions

After the word "api" the URL must have the version number, formed by the letter "v" and a sequential natural number.

Example: http://localhost/api/v1

## Microservice Root URI

Each Microservice Root URI must contain a short name of the Microservice in plural and it must return a swagger ouput with all available services.

Example: http://localhost/api/drivers -GET, must return a json answer with all services available.

Find out more: http://swagger.io

## HTTP Methods

The HTTP Methods supported should be:

* **GET** to retrieve information. Example:
http://localhost/api/drivers -GET

* **POST** to create registries. Example:
http://localhost/api/drivers -POST

* **PUT** to update registries. Example:
http://localhost/api/drivers -PUT

* **DELETE** to remove or inactivate registries. Example:
http://localhost/api/drivers/[id] -DELETE

[Back to top](#list-of-contents)

---

# Code Style

Projects should follow well known and established code-style guides. 

In the case of Java code, follow the [Google style guide](https://google.github.io/styleguide/javaguide.html) with the exception of indentation (use 4 spaces).

Java projects should use the checkstyle plugin for code style reports and checks. The plugin will run on build by default.

Add the plugin to build Gradle:

```java
apply plugin: 'checkstyle'
```

The plugin can be configured in a few ways:
```java
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

* `toolVersion` is used to specify the version to use, by default it is an older version.
* `configFile` is used to specify the XML file with the rules to follow.
* The `checkstyleMain` and `checkstyleTest` can be used to customize these two tasks. Add in excluded files here.
* A modified version of Google's Java guide can be found [here](checkstyle.xml).

[Back to top](#list-of-contents)

---

# Coverage

There should be code coverage of at least 85%, and 100% for logic in general. 

In Java the Jacoco plugin can be used to provide checks and integration with builds. This can be configured in Gradle like so:

```java
apply plugin: "jacoco"
```

This can be further configured:

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

* Specifying the output for the HTML report location.
* Defining a rule that enforces 85% coverage.
* Telling the test task to run jacocoTestCoverageVerification after it finishes.

[Back to top](#list-of-contents)

---

# Docker

## Dockerfile

A Dockerfile file should be added at the root level of the project. This is a sample default Dockerfile from Spring Boot:

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD "Replace this with the path to the jar file" app.jar
EXPOSE "ports needed, like 8080"
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```

The path to the output folder jar file should be properly added, for example: 

build/libs/new-project-0.0.1-SNAPSHOT.jar

The exposed port should match the port the applications is configured to listen to.

## docker-compose.yml

A docker-compose.yml file should be added to the applications root folder.

This is an example docker compose file which follow the structure:
* `version`:
    This field is always required.
* `services`:
    A list of the services needed for the application to run correctly.

The following example spins up `mongodb`, `rabbitmq` and the current application as Docker containers. 

By not specifying the host ports for the project services, it is left to docker compose to figure out what available ports to configure. You can find the port that docker compose assigned using the command `docker ps` and checking the PORTS entry for the Docker image.  

For example: `0.0.0.0:32777->8080/tcp` means that host port 32777 is the port used by the container, and it communicates with the port 8080 inside the container.

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

Every project should have a docker-compose.yml file and a docker-compose-local.yml.
The docker-compose.yml file should spin up all related services, including the service it is inside of,
using only images from docker-hub. This is meant to be the base that always works because it uses images 
that are built from the master branch. The docker-compose-locla.yml file should spin up all related services 
using hosted images, but spin up the service it is inside of by building it locally. This is to allow us to test 
local changes alongside of working services hosted in docker-hub. Developers are encouraged to modify the 
docker-compose-local.yml to fit their development and testing needs; but only make these changes locally, 
do not commit any changes to the branches. For example, if they need to test changes they made in two different 
repos they have locally, then they can have both of those projects be built locally instead of using the hosted 
images, and use images for the rest of the related services. 

Official documentation and more info can be found [here](https://docs.docker.com/compose/).

---

# Travis

A .travis.yml file needs to be added to the project. A minimum example for a java project is:

```yml
language: java
jdk:
  - oraclejdk8
encoding: utf8
```

This file can be configured to start services, define environment variables, run scripts, build docker, and other tasks. Other customizations and options can be found [here](https://docs.travis-ci.com/user/customizing-the-build/).

## Uploading to Docker Hub

If travis is not installed, first install it with gem:

```gem install travis```

Add the needed credentials for Docker using travis from the command line from now on.

Run this command from the project root folder for the variables `DOCKER_USERNAME` and `DOCKER_PASSWORD`. Reply *yes* when prompted:

```travis encrypt VAR_NAME="value to encrypt" --add```

These values can then be used throughout the script.

Add the following script to the `.travis.yml` file in for the project and add the corresponding Docker hub repo for the project in place of `\_DOCKERHUB_REPO_`. By default only master will be pushed, but this can be configured to push depending on other conditions like git tags.

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

[Back to top](#list-of-contents)

---

# Backing Service

We are currently using a Backing service known as the Edge service which utilizes Zuul.

More documentation and standards on Zuul itself is found [here](https://github.com/Netflix/zuul).

## Connecting services to Edge

Before configuring the service to route through Edge, make sure that you have [Docker](#docker) and [travis](#travis) set up.

Configure the `docker-compose.yml` in the Edge service root folder with the service you're connecting like so:

```yml
  nameofservice:
    image: organization/name-of-service #Image being pulled from Docker Hub
    container_name: nameofservice
    environment:
      - RABBIT_HOST=rabbitmq
      - MONGO_HOST=mongo
    ports:
      - '####'
    depends_on:
      - discovery-service #We want the service to depend on Eureka
      - rabbitmq #If the project uses RabbitMQ to communicate, be sure to make it depend on the service beforehand
      - mongo #Same goes for mongo
```

Configure the second docker compose file (which should be found within `src/test/resources`) so that the Edge can also run integration tests on the services you just added.

```yml
  nameofservice:
    image: organization/name-of-service #Image being pulled from Docker Hub
    container_name: nameofservice
    environment:
      - RABBIT_HOST=rabbitmq
      - MONGO_HOST=mongo
    ports:
      - '####' #Internal port of container
    depends_on:
      - discovery-service
      - rabbitmq
      - mongo
      #ANY OTHER SERVICES
```

## Testing the services with Edge

The Edge service uses [Palantir's DockerComposeRule](https://github.com/palantir/docker-compose-rule) to wait for services to spin up before continuing with the actual tests.

Palantir's DockerComposeRule also gives us DockerPort which helps us find the ports assigned by Docker to make testing easier.
The DockerPort is necessary when making any request to a service, so be sure to add in these lines to ensure you're accessing the right URL:  

```java
private static String nameOfServiceURL;
```

And under the `initialize()` method:  
```java
DockerPort serviceName = docker.containers().container("nameofcontainer")
            .port(INTERNAL_PORT);
        nameOfServiceURL = String.format("http://%s:%s", serviceName.getIp(),
                serviceName.getExternalPort());
        LOG.info("Trip Command endpoint found: " + nameOfServiceURL);
```

After creating a new test for your added service, be sure to add into the `@ClassRule` called `docker` the necessary code to wait for your service:
**(Use the same name that you've assigned the container within the docker-compose file)**
```java
.waitingForService("NAME OF CONTAINER", HealthChecks.toHaveAllPortsOpen())
```

The `@ClassRule` should always end with `.build()`.

This allows you to test your services locally through Gradle without having to spin up containers manually. The rule also tears down containers after all tests are executed.
* Use other methods already within the EdgeServiceIntegrationTest class as a template to writing your own tests for your specific service.
* **Note:** If your test fails during the docker compose command (or the `@ClassRule` fails in any way) the containers will not automatically be torn down. Be sure to always check for containers that are already up by using `docker ps -a`.
* If containers are present and your test is failing, use the command `docker stop $(docker ps -aq)` to **stop** all containers and `docker rm $(docker ps -aq)` to **remove** all containers. Rerun your tests again after.

---

# Logging

Logging should be provided for relevant endpoints, event handlers, and other interactions that are not automatically logged. 

Spring has some built in classes that automatically log requests arriving:

```java
@Configuration
public class RequestLoggingFilterConfiguration {

    @Bean
    public CommonsRequestLoggingFilter logFilter() {
        CommonsRequestLoggingFilter filter = new CommonsRequestLoggingFilter();
        filter.setIncludeQueryString(true);
        filter.setIncludePayload(true);
        filter.setMaxPayloadLength(10000);
        filter.setIncludeHeaders(true);
        filter.setAfterMessagePrefix("REQUEST DATA : ");
        return filter;
    }
}
```

And enable DEBUG logging in the `application.yml` file

```yaml
logging:
  level:
    org:
      springframework:
        web:
          filter:
            CommonsRequestLoggingFilter: DEBUG
```

For more customized loggings on other classes the slf4j logger can be used. Add it to the desired class:

```java
private static final Logger LOG = LoggerFactory.getLogger(CurrentClass.class);
```

After replacing `CurrentClass` with the name of the class it is located on, use it like so:

```java
LOG.info("some relevant call or logic.")	
LOG.debug("something helpful for debugging.")	
LOG.error("some critical error that happened.")	
```

[Back to top](#list-of-contents)

