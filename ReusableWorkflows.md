# Standard Project Documentation Template

## Version: `0.1.0`
## Date: `15-03-2022`
## Purpose

<!-- Include a simple statement regarding why are you writing this document.  -->

This documentation is regarding the reusable workflows. Accessing Reusable workflows across:
1. Public repositories: visible to anyone
2. Private repositories: visible only to the owner (e.g. org admins) and collaborators
3. Internal repositories: visible to anyone in the org (enterprise account)

## Scope
<!--
This describes who or under what circumstances (or both) the document applies.  -->
If we want to use a common workflow within multiple repositories, we can simply copy-paste it into those repositories. 
But, the problem with this approach is that, if any changes are made to the main workflow we have to copy-paste the 
workflow again into all the required repositories and if that is not done properly then different repositories will be having
different versions of that workflow. 

To avoid this problem, we can use the concept of reusable workflows. Reusable workflows allow us to centralize a set of common jobs.
Instead of each repo having the same set of steps for these common jobs, we can now create a centralized repo (or set of repos) that can be reused.
This keeps our workflows DRY (Don’t Repeat Yourself) and allows us to change a workflow in a single place. 
Since we can pin to a tag or branch when referencing a reusable workflow, we can ensure that we maintain backward compatibility.


## Tech Stack
<!-- Tech stack involved in the project -->
* Reusable Workflows [docs](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

## Table of Contents

* [Table of Contents](#table-of-contents)
  * [Getting Started](#getting-started)
  * [Features](#features)
    + [Feature 1](#feature-1)
  * [User Guide](#user-guide)
    + [Step 1](#step-1)
    + [Step 1.1](#step-11)
    + [Step 1.2](#step-12)
  * [Tutorials/Examples](#tutorialsexamples)
  * [Related Resources](#related-resources)


## Getting Started

## Features

### Feature 1
Reusing workflows avoids duplication. This makes workflows easier to maintain and allows us to create new workflows more
quickly by building on the work of others, just as we do with actions. Workflow reuse also promotes best practice by helping 
us to use workflows that are well-designed, have already been tested, and have been proved to be effective. 
Our organization can build up a library of reusable workflows that can be centrally maintained.

## User Guide
<!-- Describe the steps to use your project in detail and add workflow diagrams
  -->
A reusable workflow can be used by another workflow if any of the following is true:

- Both workflows are in the same repository.
- The called workflow is stored in a public repository.
- The called workflow is stored in an internal repository and the settings for that repository allow it to be accessed. 

### Step 1

Create a Repository where all the common workflows will be present. This repository can either be public or internal, 
as we cannot access reusable workflows created in a private repository across other repositories. 

Note: 
  1. According to [this](https://docs.github.com/en/actions/using-workflows/reusing-workflows#limitations), in case of 
private repositories if we create a reusable workflow, we can only access it within the same repository)
  2. In case of internal repository, we must give access/permissions.
     - Go to you Reusable Workflow repository (the one that contains the workflow you want to call)
     - Click on Settings
     - Click on Actions
     - Go at the bottom of the configuration page, in the Access section
     - Then select the proper option: "Accessible from repositories in the '\[YOUR-ORG]' organization" 

### Step 1.1
Create a reusable workflow in that repository in [this](https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow) way.

### Step 1.2
Now, in whichever repository (public/private/internal) you want to use the reusable workflow, create a caller workflow in [this](https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow) way.


## Tutorials/Examples
<!-- Add a comprehensive end-to-end example of your working project -->

For example purpose,
1. I have created a public repository named "main_repo".
2. In this repository I have created a reusable workflow named "greet.yml" as follows:
```yaml
name: Greetings 

on:
  workflow_call:
    inputs:
      name:
        description: Name of a person
        required: true
        type: string
        default: 'Anonymous'
    
    outputs:
      firstword:
        description: "The first output string"
        value: ${{ jobs.build.outputs.output1 }}

    
jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      output1: ${{ steps.greet.outputs.firstword }}

    steps:
    -   name: Check out HEAD
        uses: actions/checkout@v2

    -   name: Greet a person
        id: greet
        run: |
          echo "::set-output name=firstword::Hello ${{ inputs.name }}"
```
This workflow simply takes name as an input from the caller workflow and says hello to that person in output.

3. To call this reusable workflow in other repository, I have created a caller workflow in that repository named "test-greet.yml" as follows:
```yaml
name: Testing greet workflow

on:
  push:
      branches: [master]

jobs:
  job1:
    uses: vaishnavimuley22/main_repo/.github/workflows/greet.yml@master
    with:
      name: 'Devesh'

  job2:
    runs-on: ubuntu-latest
    needs: job1
    steps:
      - run: echo ${{ needs.job1.outputs.firstword }}
```
With the help of this workflow, I am simply calling greet workflow by passing 'Devesh' as input to the workflow and printing out the output provided by the workflow.

## Related Resources

<!-- Include references and links to relevant materials which may be helpful for executing the process. -->
1. [Creating a reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow)
2. [Calling a reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows#calling-a-reusable-workflow)
3. [Steps to give permission to internal repositories](https://github.community/t/reusable-workflows-internal-repositories-feature-removal/214748/5)
