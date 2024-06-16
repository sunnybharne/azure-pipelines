# Azure pipelines

<details>
  <summary>Pipeline basics</summary>

- Pipeline is collection of stages, stages are collection of jobs, jobs are collection of steps.
- Each Job runs on one agent.
- Approvals can be added to stages for manual intervention.
- Deployment in yaml referes to deployment jobs and its action of running a sequencial task for one stage .
- Deployment groups are collection of target machines where the application is deployed [Strategies like run once, rolling, and canary can be used for deployment jobs].
- Environments are collection of resources where the application is deployed.
- Jobs are collection of steps that run sequentially on the same agent, there can be agentless jobs too.
- Release is versioned set of artifacts specified in a pipeline for a deployment to an environment.
- Run is a single execution of a pipeline.
- Step is a single task that is executed by the agent.
- Script is a sequence of commands that are executed by the agent.
- Task is a pre-packaged script that performs an action in a pipelines.
- Library includes secure files and variable groups. Secure files are a way to store files and share them across pipelines.
</details>

<details>
  <summary>Trigger basics</summary>

- Triggers can't use variables in triggers.
- Cannot specify triggers in the template files.
- There are two types of triggers , Build Triggers or CI Triggers and Release Triggers or CD Triggers.

```yaml
name: my-first-azure-pipeline # Name of the pipeline

trigger: none # This will not run the pipeline automatically, you have to run it manually.
trigger: # This is a CI or Build trigger, This will run when there is a commit to the branch or if you push specified tags.
batch: true # This will run the pipeline in sequence if multiple commits are pushed. Address caution when using this, as you wont be able to run t
    branches:
        include:
      - main
      - feature/* # Wildcard can include * , ** and ? characters * meaning any number of characters and ? meaning any single character, If you start your pattern with * in a YAML pipeline, you must wrap the pattern in quotes, like "*-releases" can be used on branches and paths.
    exclude:
      - wip/*
  paths:
    include:
      - pipelines/my-first-azure-pipeline*
    exclude:
      - README.md
tags: # This is a tag trigger, This will run when you push a tag that matches the pattern. If you don't specify any tag triggers, then by default, tags will not trigger pipelines.
    include:
      - v1.*
    exclude:
      - v1.0
#Pr Triggers
pr:
 branches:
   include:
#      - main
#    exclude:
#      - wip/*
#  paths:
#    include:
#      - pipelines/my-first-azure-pipeline*
  #   exclude:
  #     - README.md
  # tags:
  #   include:
  #     - v1.*
  #   exclude:
  #     - v1.0

resources: # This is redundant here as this is the default behaviour
    - repo: self

```

- To skip a pipeline run, you can include the following in the commit message:
- [skip ci] or [ci skip]
- skip-checks: true or skip-checks:true
- [skip azurepipelines] or [azurepipelines skip]
- [skip azpipelines] or [azpipelines skip]
- [skip azp] or [azp skip]
- NO_CI
- Adding conditions to the pipelines
- condition: and(succeeded(), ne(variables['Build.Reason'], 'PullRequest'))

</details>


<details>
  <summary>Agent pool</summary>

```yaml
pool: # Define selfhosted pool for the pipeline
   name: 'selfhosted'
     
pool: # Define Microsoft hosted pool for the pipeline
    vmImage: 'ubuntu-latest' # you can use 'windows-latest' for Windows and 'macOS-latest' for macOS 

strategy: # Parallel jobs on different OS
    matrix:
      linux:
        imageName: "ubuntu-latest"
     mac:
        imageName: "macOS-latest"
      windows:
        imageName: "windows-latest"
    maxParallel: 3

pool:
    vmImage: $(imageName)
strategy: # Parallel jobs on different JDK versions
    matrix:
      jdk10:
        jdkVersion: "1.10"
      jdk11:
        jdkVersion: "1.11"
    maxParallel: 2

strategy: # Parallel jobs on different OS and JDK versions
    matrix:
      jdk10_linux:
        imageName: "ubuntu-latest"
        jdkVersion: "1.10"
      jdk11_windows:
        imageName: "windows-latest"
        jdkVersion: "1.11"
    maxParallel: 2
 
  pool:
    vmImage: $(imageName)
  pool:
   name: 'selfhosted'
```
</details>
