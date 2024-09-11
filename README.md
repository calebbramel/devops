# Overview
Azure DevOps (ADO) is a Software-as-a-Service (SaaS) offering from Microsoft and is the preferred way to host git repositories. It is serverless, safe, secure, and scalable. Being an Azure offering, it integrates easily with other resources in the Azure environment. ADO can be integrated with Azure Pipelines and enables full traceability from code commit to deployment.

# Source Control
## Overview
This section will provide references to documentation to manage the day-to-day activities in Azure DevOps using Git.
## Connect Visual Studio to ADO
This step will be needed to perform any ADO actions using git commands.
[https://docs.microsoft.com/en-us/azure/devops/repos/git/gitquickstart?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/gitquickstart?view=azure-devops&tabs=visual-studio)

## Create a Git Repository
A user with admin rights will generally create an empty repository to serve as the git remote.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/creatingrepo?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/creatingrepo?view=azure-devops&tabs=visual-studio)

## Git Clone Repository
Users will clone a repository to make changes locally.
[https://docs.microsoft.com/en-us/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio)

## Git Commmit
After sufficient changes have been made, save changes to the git repository on the user's machine.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/commits?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/commits?view=azure-devops&tabs=visual-studio)

## Git Push
Once changes have been made locally, they can be pushed to ADO. This can result in merge issues.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/pushing?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/pushing?view=azure-devops&tabs=visual-studio)

## Git Fetch / Git Pull
To mitigate merge issues, users should regularly update their local repository with the latest changes.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/pulling?view=azure-devops&tabs=visual-studio](https://docs.microsoft.com/en-us/azure/devops/repos/git/pulling?view=azure-devops&tabs=visual-studio)

## Trunk Based Branching and Merging
A best-practice to making repository branches is following the trunk strategy.

From [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/):
This method has developers collaborate on code in a single trunk branch (referred to as ‘trunk’), and resist any pressure to create other long-lived development branches by employing documented techniques. They therefore avoid merge hell, do not break the build, and live happily ever after.

### Pull Request
When code is ready to be reviewed and merged with an upstream branch, a Pull Request (PR) must be created. A PR is used to review code, make comments, approve code changes etc. in an open and transparent way. Everything is also documented in the PR for audit and compliance purposes.
[https://docs.microsoft.com/en-us/azure/devops/repos/git/pullrequest?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/repos/git/pullrequest?view=azure-devops)

### Use-cases
#### Work Item ADO-123456 Assigned
1.	Engineer creates the ‘/features/joe/123456-do-stuff’ branch from the trunk (develop). 
2.	Engineer completes work and submits a PR to merge feature branch back into the trunk.
3.	Once PR is approved, quality gates pass, and merge is successful, remove feature branch. 

#### Release Branch Created
A release branch will be necessary when either:
1. All work items are completed that will be included in the release.
2. Work on the next release needs to start.
    a. It's best to cut the branch and make a new one at this time. Additional or unfinished work can be cherry-picked into a new branch. 

#### Defect Found During Regression Testing
1.	Engineer creates feature branch from trunk (develop)
2.	Engineer fixes defect and submits a PR back to trunk (develop)
3.	Fix is validated in Dev and Test by QA team. 
4.	PR is cherry picked into the release branch. 
5.	Regression test team validates the bug in the release.

This approach allows engineers to work the same way regardless of type of work, and allows the code to benefit from the same CI/CD, QA, and security processes. Since the regression test team will be validating all code in the release, there is a low chance the issue will persist.

Two outliers will require the engineer to fix the code on each branch and use separate PRs.
1. The trunk code is significantly different and does not include the defect.
2. The trunk code has the defect, but is fixed in a different way.

It's not advised to change release and merge to trunk. This could result in merge conflicts and bugs that are unseen due to the difference in release testing versus trunk. If code from the release must be brought into trunk, the safest option is to cherry-pick PRs from release to trunk, but the risks still remain. 

#### Fast-follow Release
Fast-follow releases generally happen to correct low-to-medium defects found during regression testing, and is deemed more important to release and follow-up with a new release quickly after.

1.	A release branch R1.2 is cut from R1.1
2.	Engineer creates feature/defect branch from trunk (develop)
3.	Engineer fixes defect and submits PR back to trunk (develop)
4.	Fix is validated in Dev and Test by QA team. 
5.	PR is cherry picked into the R1.2 release branch. 
6.	The regression test team validates the bug in R1.2
7.	R1.2 can be deployed to prod after all required defects have been completed

#### Production Hotfix
Hotfixes occur when a high-priority defect has been identified in production and will be fixed ASAP.

1.	A new release/hotfix branch (R1.1.1) is created from the release branch currently deployed to production (R1.1)
2.	Engineer creates a feature branch from trunk (develop)
3.	Engineer fixes defect and submits PR back to trunk (develop)
4.	Fix is validated in Dev and Test by QA team. 
5.	PR is cherry picked into the hotfix R1.1.1 release branch.
6.	PR is cherry picked into the R1.2 release branch.
    a. This requires manual confirmation that the PR is cherry-picked to R1.2 unless regression test suite is updated to catch defect
7.	QA team is validating the fix in R1.1.1
8.	R1.1.1 is deployed to PROD

### Key Considerations
#### Integration Testing
Catch issues early and maintain quality by using integration and regression testing within the CI/CD pipeline.
#### Documentation
Ensure that all team members are informed about the branching strategy, processes and guidelines, including when to create and merge branches.
#### Review
All PR’s into Release branches should be reviewed by Dev Leads or architects to prevent any unexpected changes to the releases.  
#### Bug Fix Management
Develop a clear strategy for managing bug fixes, whether by fixing in the main branch first and cherry-picking to release branches or vice versa, to ensure consistency and quality.
#### Feature Toggles
Consider using feature toggles to manage incomplete features in the main branch, allowing for continuous integration without affecting production code.
[https://docs.github.com/en/get-started/quickstart/github-flow](https://docs.github.com/en/get-started/quickstart/github-flow)
[https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/repos/git/git-branching-guidance?view=azure-devops)

## Branch Policies
### Overview
Branch policies are essential technical controls used in promoting best practices in code review, testing, and build processes, ultimately enhancing the overall reliability and maintainability of the software.

In ADO, branch policies can be configured at the Project Level and then inherited by all repos in the project, or they can be configured at the specific repo level.

#### Enforce on All Repositories
Go to Project Settings -> Repositories -> Policies -> Add (plus sign):
Then select the second radio button and type in branch name, then hit create.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#configure-branch-policies](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#configure-branch-policies)

#### Enforce on Select Repositories
Repository-level policies require going to the repository level and viewing all branches. Locate the desired branch and add policies via the three-button menu.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#configure-branch-policies](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#configure-branch-policies)

### Minimum Viewers
At least 1 person (not the change submitter) should be required to review the code to ensure quality.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#require-a-minimum-number-of-reviewers](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#require-a-minimum-number-of-reviewers)

### Pull Request Comment Resolution
Pull request should be blocked from being merged until comments are resolved, as this is a major workflow within distributed teams.
[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#check-for-comment-resolution](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#check-for-comment-resolution)

### Build Validation
This requires the code to successfully build for a PR to complete.
A build definition must exist before a policy enforcing a build validation can exist.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#build-validation](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#build-validation)

### Automatically Include Code Reviewers
Static or dynamic groups of users can be assigned as reviewers to be added when a PR is submitted.

[https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#automatically-include-code-reviewers](https://docs.microsoft.com/en-us/azure/devops/repos/git/branch-policies?view=azure-devops#automatically-include-code-reviewers)

## Repository Structures
### Overview
In DevOps we try to follow the DRY (Don’t Repeat Yourself) practice. Much like applications use contracts and abstraction, we can do the same thing with our pipelines and infrastructure.

Having IaC in the application code allows ephemeral infrastructure to exist that will be destroyed when the branch is deleted. The common infrastructure as code will live on the main branch.

```
App Repository                              (source: hello-world-demo)
* /hello-csharp/                            (source: hello-world-demo)
* /YAML                                     (source: hello-world-demo)
* /IaC                                      (source: hello-world-demo)
* /IaC/modules                              (source: repo-infrastructure)
* /build/dotnetwebbuild.yml                 (source: repo-cicd-common)
* /deploy/azure-deploy-with-terra-apply.yml (source: repo-cicd-common)
* /terraform-yaml/terraform-init.yml        (source: repo-cicd-common)
```
# Azure Build / Release Pipelines (CI/CD)
Azure Pipelines is a service within ADO that can be used to build test, and deploy code using YAML.
These pipeliens execute on either serverless agents or hybrid (self-hosted) agents.

## Variable Groups
Variable groups centralize pipeline configuration values. These can be scoped to the application, environment, or both, and can be plaintext or secured with encryption.

Common-{env}: All apps in an environment
{app}-{env}-Variables: App and Environment
{task-name}: One-off for a specific task or stage apart from the other groups

## Considerations
1. The same variable value should not exist in multiple variable groups.
2. Secrets should be encrypted or pulled from Azure Key Vault.

## Creating a Pipeline
### Overview
A repository must exist in a team project to create a pipeline.

One pipeline should be used to build and deploy each workload. This will look different for IaC pipelines.

A workload should be built once and deployed many times.

[https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/key-pipelines-concepts?view=azure-devops)

### Creaing a Pipeline from an Existing YAML
1. In ADO, go to the pipelines blade and select New Pipeline
2. Select the repository type
3. Navigate to the repository this pipeline should target
4. Select Existing Azure Pipelines YAML file
5. Review and select Save or Run.

#### Trigger
Trigger is used to specify the branches that will automatically start a pipeline run when changes are pushed to them, or a cron schedule to run on a recurring basis. By default, the trigger will run on changes to any branch in the repository, but it can be customized to include specific branches or filepaths.

```
trigger:
  branches:
    include:
      - develop
  paths:
    include:
      - src/*
```
#### Variables
Variables store and manage values to be reused in pipelines. A duplicate variables should not exist between pipelines, as this can cause issues if one is changed, and it is assumed both will always have the same value.

Pipeline level variables should therefore only be used for the inner workings of that pipeline. Any variables that are shared belong in variable groups. 

```
variables:
  - group: Variable-Group-1
  - name: branchName
    value: $(Build.SourceBranchName) # Environment Variable
```
#### Build Stage
The build stage compiles code into executables or libraries. This ensures that the code is free from syntax errors and ready for further testing and deployment. This stage involves compiling, resolving dependencies, running build-time scripts, and generating build artifacts. This lays the foundation for subsequent stages by producing reliable and tested outputs.

The output from build is called an "Artifact". These are usually compiled binaries and dependencies. To avoid having to build and compile more than once, we copy to a publish location for re-use.

```
- stage: Build
  displayName: 'Build Stage'
  jobs:
  - job: Build
    displayName: 'Build Job'
    steps:
    - task: UseDotNet@2
      inputs:
        packageType: 'sdk'
        version: '6.0.1'
        installationPath: $(Agent.ToolsDirectory)/dotnet
    - script: | 
        dotnet build --configuration $(buildConfiguration)
      displayName: 'Build project'

    - task: PublishBuildArtifacts@1
      displayName: 'Publish Artifacts'
      inputs:
        pathToPublish: '$(System.DefaultWorkingDirectory/output)'
        artifactName: 'myArtifact'
        publishLocation: Container

```
#### Deployment Stages
The steps facilitate application delivery across various environments. Separating this work into stages allows controlled testing, validation, and release.

Deployment stages should correspond to the respective ADO environment to enforce the permissions and checks defined.

```
- stage: DeployToDev
  pool: my-hybrid-agents
  displayName: 'Deploy to Dev'
  dependsOn: Build
  condition: succeeded()
  jobs:
  - template: azure-pipelines-deploy-template.yml
    parameters:
      environment: 'app1-dev'
      variablesGroup: app1-dev-variables'
      variablesGroupCommon: 'Common-dev'
      serviceConnectionName: 'Azure-Subscription1'
```
Note that the condition will only fire on the Build stage's success.

### Pipeline Workflows
Multistage YAML pipelines can be used to perform different workflows. This allows for a pipeline per workload, that can perform tasks and route to stages depending on the branch and how the stages execute.

#### Pull Request Build
A Pull Request build is part of the PR process and should ensure code compiles and unit tests are successful, then stop.

The simplest way to do this is to define and evaluate 'isPR' variable.

We can add a condition to only run a stage if the last stage succeeded, and the task isn't a PR.

```
  condition: and(succeeded(), ne(variables['isPR'], 'true'))
```

#### Continuous Integration Builds
We want the CI build to execute once a PR is merged into 'develop' branch, and deploy to 'dev' environment.

Below, we'll declare a variable for this based on if the source branch isn't a production release:
```
variables:
  - name: isDevelop
    value: $[not(startsWith(variables['Build.SourceBranch'], 'ref/heads/Releases))]
```
Then, we'll edit our condition to match.
```
  condition: and(succeeded(), eq(variables['isDevelop'], 'true'), ne(variables['isPR'], 'true'))
```
#### Release Builds
Release builds are build from a release branch and target production deployment.

Again, we can define this based on the branch name.
```
variables:
  - name: isDevelop
    value: $[not(eq(variables['Build.SourceBranchName'], 'Develop'))]
```

### Naming and Organization Structure
Define a naming scheme for pipelines. This could be the repository name, the application or infrastructure name, or the activity being performed.

By default, the pipeline name will match the repo the YAML definition is stored in.

Pipelines can be stored in folders. It's advised to use the following:

Infrastructure - for Azure Infrastructure or other cloud connections
Databases - Pipelines building or deploying migration packages 
AKS - Pipelines building and deploying services or APIs in containers

### Pipeline Documentation
Tasks: [https://docs.microsoft.com/en-us/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=yaml](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=yaml)
Variables: [https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch)
Build Triggers: [https://docs.microsoft.com/en-us/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=yaml](https://docs.microsoft.com/en-us/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=yaml)
Build Artifacts: [https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/build-artifacts?view=azure-devops&tabs=yaml](https://docs.microsoft.com/en-us/azure/devops/pipelines/artifacts/build-artifacts?view=azure-devops&tabs=yaml)
Release Triggers: [https://docs.microsoft.com/en-us/azure/devops/pipelines/release/triggers?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/triggers?view=azure-devops)
Release Variables: [https://docs.microsoft.com/en-us/azure/devops/pipelines/release/variables?view=azure-devops&tabs=batch](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/variables?view=azure-devops&tabs=batch)
Release Artifacts: [https://docs.microsoft.com/en-us/azure/devops/pipelines/release/artifacts?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/artifacts?view=azure-devops)
Release Approvals and Gates: [https://docs.microsoft.com/en-us/azure/devops/pipelines/release/approvals/?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/approvals/?view=azure-devops)
Release Pipeline YAML: [https://docs.microsoft.com/en-us/azure/devops/pipelines/process/stages?view=azure-devops&tabs=yaml](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/stages?view=azure-devops&tabs=yaml)
YAML Templates: [https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/templates?view=azure-devops)
Token Replacement: [https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens&ssr=false#overview](https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens&ssr=false#overview)
Pipeilne JSON to Pipeline Varilable:
[https://marketplace.visualstudio.com/items?itemName=OneLuckiDev.json2variable](https://marketplace.visualstudio.com/items?itemName=OneLuckiDev.json2variable)
YAML Runtime Variables: [https://docs.microsoft.com/en-us/azure/devops/pipelines/process/runtime-parameters?view=azure-devops&tabs=script](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/runtime-parameters?view=azure-devops&tabs=script)
# Infrastructure as Code (IaC)
IaC allows infrastructure to be deployed via templates and scripts stored in source control, and managed in a similar way to application code. This means you can use PRs, approval gates, and pipelines with infrastructure code such as Terraform. 

Subscription Governance: [https://docs.microsoft.com/en-us/azure/architecture/cloud-adoption-guide/subscription-governance](https://docs.microsoft.com/en-us/azure/architecture/cloud-adoption-guide/subscription-governance)
## Prerequisites
Azure Subscription
Visual Studio Code: [https://code.visualstudio.com/](https://code.visualstudio.com/)
    Terraform and Golang VS code extensions
Terraform CLI: [https://www.terraform.io/downloads](https://www.terraform.io/downloads)

## Terraform Modules
Terraform can be broken into modules to group commonly paired resources. For example, you could create a terraform module for AKS, blob storage, keyvault, networking, resource groups, web application firewall, etc.

## Environment Specific Variables
The best way to manage environment configurations are `{env}.tfvars` files. This allows deploying to different environments by changing the tfvars file targeted.

## Accessing Modules
Each environment should be maintained in its own repository, ensuring separation between infrastructure and application code. 

Accessing a module from a Git repository would require changing the local Terraform source refernece to the Git URL rather than a path. 

To access public repositories such as in GitHub, HTTPS is the easiest method.
For ADO or private GitHub repos, SSH wil allow secure authentication.  

[https://samcogan.com/using-terraform-modules-from-git-in-azure-devops/](https://samcogan.com/using-terraform-modules-from-git-in-azure-devops/)

## IaC Pipeline Overview
Most IaC is broken into two stages: Terraform Plan and Terraform Apply.

### Terraform Plan
Ensure any modifications to the Terraform state are reviewed before implementation.
1. Checkout Repository: Pull the latest version of the code, ensuring that the pipeline works with the most recent updates to configuration files.
2. Fetch Azure Credentials: Retrieves the necessary credentials to authenticate with Azure.
3. Display Terraform Version: Output to identify or avoid compatability issues.
4. Terraform Init: Set up working directory, download plugins, and configures the backend to store the Terraform state.
5. Terraform Plan: Generates an execution plan to show the actions Terraform will take when applying the configuration. The plan is reviewed to ensure that the proposed changes align with the desired infrastructure state.
### Terraform Apply
Once the plan is reviewed and approved, the Terraform Apply stage is executed.
1. Terraform Apply: This task creates, updates, or deletes resources as necessary to match the desired state from the plan file.

## Executing IaC Pipeline Manually
1. Navigate to the Azure Pipelines Page
2. Select your desired pipeline and click Run
  a. Select the target branch/tag combination for the pipeline to run on
  b. Select/Deselect Stages (if applicable)
3. Review and Select Run

# Agents
All jobs in a pipeline have to run on either a Microsoft-hosted agent or a hybrid on-premesis agent.

## Microsoft-Hosted Agents
+ Good for tasks that touch public resources or don't require in-house software
+ Do not need maintenance
+ Supports operating Systems like MacOs, Windows, Ubuntu
+ 1800 free minutes a month
+ Common software is pre-installed
+ Can install software from public repositories if unattended
- Can be slow on large repositories since each job is a fresh download
- Limit of 1 free parallel pipeline
- Limit of 10GB storage per job
- Limited to Azure VM DS2_v2 SKU
## Self-Hosted Agents
+ More control over data and infrastructure
+ Unlimited Build Time
+ Decreased build time since code is already downloaded after the first run
+ Free parallel pipeline for each Microsoft Enterprise Subscriber in ADO
- Agents require support, monitoring, and maintenance
- Limited to Operating Systems your company Supports
- You must manage software and licensing on each server 
- Permissions are scoped to the account the agent runs as
- Agent must have access to your private network
Self-Hosted agents: [https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops#install](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops#install)
Agent Pools: [https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/pools-queues?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/pools-queues?view=azure-devops)
Agent Capabilities: [https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops#capabilities](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents?view=azure-devops#capabilities)
Deployment Groups for Parallel pipeline jobs: [https://docs.microsoft.com/en-us/azure/devops/pipelines/release/deployment-groups/?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/deployment-groups/?view=azure-devops)
# Test Automation
## Overview
Test automation can save considerable time and resources in the development lifecycle.

Automated functional testing is commonly used in regression testing, smoke testing, environment shakeout, or any other repetitive use-case. 

### Test Automation Objectives
* Reduce duration of testing phases
* Enable 24/7 testing
* Provide consistent test results
* Increase precision of tests
* Increase test coverage by automating repetitive tests
* Allow more frequent regression testing, sanity testing
* Shifting QA left to require less toil
### Software Devlopment Engineer in Test (SDET) Role Objectives
* Develop and deliver automated, regression, smoke and/or functional test & scripts
* Enable developers to write more effective unit tests by encouraging continuous testing
* Maintaining existing automated regression, smoke and/or functional test scripts
* Handle ad-hoc automation requests from the QA team
* Execute functional, performance, regression tests through Azure Pipelines as well as monitor performance of scripts / results

### Benefits
+ Help reduce the overall test execution effort 
+ Potential to shorten the test cycle duration
+ Redirect existing QA resources to high-value testing activities
+ Reduce human error as automation will always test the script the same way
+ Consistent test results
+ Increase test coverage by automating repetitive tests 
+ Potential to reduce testing costs for future application releases and/or maintenance work
+ Increase test coverage without increasing overall FTE count or test project duration
+ Reduce data management costs via automated test data creation??
+ Increase ability to support testing across multiple test environments with limited resources

## Test Automation Scope
* Unit Testing (JUnit, NUnit/xUnit, etc)
  * Isolates and tests a specific and small functional unit of source code.
  * Unit tests are created and executed by the developers, where coverage is determined either line by line, methods, conditional branching, etc.
  * Unit Test plans are created and maintained by the relevant development teams
* Functional/UI Testing (Jest, React Testing Library)
  * Tests the client application as a simulated user through the browser. 
* Develop an automation framework that can be used across the application/s being tested
* Implement test scripts to perform the necessary functional steps and validations as required by the test script ensuring that the application behavior meets the expected results
* Services Layer Testing (Custom Solution using Groovy, Postman)
  * Tests the services layer (integration layer i.e., serverless restful services, soap requests by utilizing HTTP (Hypertext Transfer Protocol) clients to invoke requests and validate the responses returned 
* Performance Testing (Locust.io?)
  * Load/Stress/Soak testing to determine how a system performs in terms of responsiveness and stability under a particular workload, often performed at the service level.

# Additional ADO References
Main Documents: [https://docs.microsoft.com/en-us/azure/devops/index?view=azure-devops](https://docs.microsoft.com/en-us/azure/devops/index?view=azure-devops)
What is Azure Pipelines?: [https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops](https://learn.microsoft.com/en-us/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops)
Adding Users: [https://learn.microsoft.com/en-us/azure/devops/organizations/security/add-users-team-project?view=azure-devops&tabs=preview-page](https://learn.microsoft.com/en-us/azure/devops/organizations/security/add-users-team-project?view=azure-devops&tabs=preview-page)
Access Levels: [https://learn.microsoft.com/en-us/azure/devops/organizations/security/access-levels?view=azure-devops](https://learn.microsoft.com/en-us/azure/devops/organizations/security/access-levels?view=azure-devops)
