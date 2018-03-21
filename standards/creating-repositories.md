# Guidelines For Creating a New Repository

It is awesome that you are creating a new repository for AIST, but before you do this there are some guidelines we would like you to follow.

## Naming Conventions
Project names should be all lower-case with dashes instead of spaces. Project should not begin with numbers or symbols or contain symbols.
Examples:
My New Project => my-new-project

### Repository Tags
Each repository needs at least one tag to identify which project it belongs to.
* perception -- tag: aist-perception
* microservice-- tag: aist-sandbox
* aide-- tag: aist-aide

## Pipeline First
We use Travis for our pipelines, after your first commit that includes tests you will need to setup a pipeline by enabling Travis for the new repository then configure it with a Travis yaml file.

Enable Travis for your repository https://docs.travis-ci.com/user/getting-started/ 
Add the Travis badge to your readme.md https://docs.travis-ci.com/user/status-images/ 
Configure your project’s Travis.yaml for your language, frameworks, and tests https://docs.travis-ci.com/user/docker/ 
Note, if this is a Gradle based project you will need to execute the following two commands for Travis to have the proper execution permissions.

```bash
chmod +x ./gradle/wrapper/gradle-wrapper.jar
```
```bash
chmod +x ./gradlew
```

## Docker First
You will also need to Dockerize your app. There are tons of pre-configured containers out there and at minimum your app will need to launch, with ports exposed in Docker. It would also be great if you tied your app into our Docker Compose yaml config though.
https://github.com/wsargent/docker-cheat-sheet 

## GitHub Settings
* There are a few settings you will need to enable / change which we require for all the repositories.
* Go to the repository Settings 
* Navigate to Collaborators  
* Add your team to the collaborator teams 
* Give the team write access 
* Go to Branches  
* Choose the Master Branch under Protected Branches 
* Add the following settings and Save 
  * Require pull request reviews before merging 
  * Require status checks to pass before merging 

