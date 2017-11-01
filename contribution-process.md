# Guidelines for Contributing

The first step to contributing to AIST is that you need to ensure that you have registered as a contibutor on the AIST website. Look for how to do this on the [Welcome](README.md) page. We use Git Flow process for contributing, below you will find a basic guide to get started with. If you want to read about Git Flow in more detail see the entire guide here https://datasift.github.io/gitflow/IntroducingGitFlow.html.
## Create a story
Create a story in under your project, drag it into "In Progress".

## Create or Clone a Repository
If you want to get started with your own repository see our [Guidelines for Creating Repositories](creating-repositories.md) page. If you would like to contribute to an existing project then the first thing you will need to do is clone the repository.

```bash
git clone <repo_address>
```

## Create a branch
By default the branch will be master, you will need to create your working branch for your story.

```bash
git checkout -b my-branch-name
```

## Create commits
Create commits as you see fit while you are implementing your feature. 

```bash
git add ./my-file.py
git commit -m "my file implementation"
```

## Create a Pull Request
A pull request (PR) is a request to review your changes as a differential to master or some branch.

Steps:
* From the repository GitHub webpage select Pull Requests
* Click the "New Pull Request" button
* Select the branch you want to have reviewed
* In the review select your team as reviewers
* Create the pull request

### Requirements for Merging
There are two requirements that will allow you to merge.
* Your [Travis CI](https://travis-ci.org/AITestingOrg/) is green (passing).
* At least one other contributor has approved your pull request.

## Merge
Once you have met all the requirements you are free to merge your branch / pull request into the  main branch.

## Close your story
If the automation hasn't already moved your story, go to your project and move your story to Done. Now go ahead and pull another story :)