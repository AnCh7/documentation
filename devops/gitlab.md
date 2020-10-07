# Introduction to CI/CD with GitLab

The continuous methodologies of software development are based on automating the execution of scripts to minimize the chance of introducing errors while developing applications. They require less human intervention or even no intervention at all, from the development of new code until its deployment.

It involves continuously building, testing, and deploying code changes at every small iteration, reducing the chance of developing new code based on bugged or failed previous versions.

### Continuous Integration

Developers push code changes every day, multiple times a day. For every push to the repository, you can create a set of scripts to build and test your application automatically, decreasing the chance of introducing errors to your app.

### Continuous Delivery

[Continuous Delivery](https://continuousdelivery.com/) is a step beyond Continuous Integration. Your application is not only built and tested at every code change pushed to the codebase, but, as an additional step, it’s also deployed continuously, though the deployments are triggered manually.

This method ensures the code is checked automatically but requires human intervention to manually and strategically trigger the deployment of the changes.

### Continuous Deployment

[Continuous Deployment](https://www.airpair.com/continuous-deployment/posts/continuous-deployment-for-practical-people) is also a further step beyond Continuous Integration, similar to Continuous Delivery. The difference is that instead of deploying your application manually, you set it to be deployed automatically. It does not require human intervention at all to have your application deployed.

### How GitLab CI/CD works

To use GitLab CI/CD, all you need is an application codebase hosted in a Git repository, and for your build, test, and deployment scripts to be specified in a file called [`.gitlab-ci.yml`](https://docs.gitlab.com/ee/ci/yaml/README.html), located in the root path of your repository. 

In this file:

- define the scripts you want to run
- define include and cache dependencies
- choose commands you want to run in sequence and those you want to run in parallel
- define where you want to deploy your app
- specify whether you will want to run the scripts automatically or trigger any of them manually.

Once you’ve added your `.gitlab-ci.yml` configuration file to your repository, GitLab will detect it and run your scripts with the tool called [GitLab Runner](https://docs.gitlab.com/runner/), which works similarly to your terminal. 

The scripts are grouped into **jobs**, and together they compose a **pipeline**. A minimalist example of `.gitlab-ci.yml` file could contain:

```yaml
before_script:
  - apt-get install rubygems ruby-dev -y

run-test:
  script:
    - ruby --version
```

GitLab CI/CD not only executes the jobs you’ve set but also shows you what’s happening during execution, as you would see in your terminal:

[![job running](.gitlab-images/job_running.png)](https://docs.gitlab.com/ee/ci/introduction/img/job_running.png)



You create the strategy for your app and GitLab runs the pipeline for you according to what you’ve defined. Your pipeline status is also displayed by GitLab:

[![pipeline status](.gitlab-images/pipeline_status.png)](https://docs.gitlab.com/ee/ci/introduction/img/pipeline_status.png)



At the end, if anything goes wrong, you can easily [roll back](https://docs.gitlab.com/ee/ci/environments/index.html#retrying-and-rolling-back) all the changes:

[![rollback button](.gitlab-images/rollback.png)](https://docs.gitlab.com/ee/ci/introduction/img/rollback.png)

### Basic CI/CD workflow

![GitLab workflow example](.gitlab-images/gitlab_workflow_example_11_9.png)

### Detailed CI/CD workflow

![Deeper look into the basic CI/CD workflow](.gitlab-images/gitlab_workflow_example_extended_v12_3.png)

1. Verify
   - Automatically **build and test** your application with Continuous Integration.
   - Analyze your source code quality with [GitLab Code Quality](https://docs.gitlab.com/ee/user/project/merge_requests/code_quality.html).
   - Determine the **browser performance** impact of code changes with [Browser Performance Testing](https://docs.gitlab.com/ee/user/project/merge_requests/browser_performance_testing.html). 
   - Determine the **server performance** impact of code changes with [Load Performance Testing](https://docs.gitlab.com/ee/user/project/merge_requests/load_performance_testing.html). 
   - Perform a series of **tests**, such as [Container Scanning](https://docs.gitlab.com/ee/user/application_security/container_scanning/index.html) , [Dependency Scanning](https://docs.gitlab.com/ee/user/application_security/dependency_scanning/index.html) , and [Unit tests](https://docs.gitlab.com/ee/ci/unit_test_reports.html).
   - Deploy your changes with [Review Apps](https://docs.gitlab.com/ee/ci/review_apps/index.html) to **preview** the app changes on every branch.
2. Package
   - Store **Docker** images with [Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/index.html).
   - Store NPM packages with [NPM Registry](https://docs.gitlab.com/ee/user/packages/npm_registry/index.html). 
   - Store **Maven** artifacts with [Maven Repository](https://docs.gitlab.com/ee/user/packages/maven_repository/index.html). 
   - Store **Conan** packages with [Conan Repository](https://docs.gitlab.com/ee/user/packages/conan_repository/index.html). 
3. Release
   - Continuous Deployment, **automatically deploying** your app to production.
   - Continuous Delivery, **manually** click to **deploy** your app to production.
   - Deploy static **websites** with [GitLab Pages](https://docs.gitlab.com/ee/user/project/pages/index.html).
   - Ship features to only a portion of your pods and let a percentage of your user base to visit the temporarily deployed feature  with **[Canary Deployments](https://docs.gitlab.com/ee/user/project/canary_deployments.html).** 
   - Deploy your features behind **[Feature Flags](https://docs.gitlab.com/ee/operations/feature_flags.html).** 
   - Add **release notes** to any Git tag with [GitLab Releases](https://docs.gitlab.com/ee/user/project/releases/index.html).
   - View of the **current health and status** of each CI environment running on Kubernetes with [Deploy Boards](https://docs.gitlab.com/ee/user/project/deploy_boards.html). 
   - Deploy your application to a **production environment** in a Kubernetes cluster with [Auto Deploy](https://docs.gitlab.com/ee/topics/autodevops/stages.html#auto-deploy).